<template>
  <div id="OnboardingCard">

    <!-- will be used as teleport target -->
    <div v-if="conversation" class="chat-wrapper">
      <div class="messages-scroll-viewer">
        <section id="messages" class="messages">
          
          <!-- Greetings From Walfred --> 
          <div class="onboarding-head">
            <div>
              <h1>Ahoi</h1>
            </div>
            <Fish />
          </div>

          <div class="inner-messages">
            <!-- Here the conversation will Walfred will take place -->
            <div
              v-for="(message, index) in conversation"
              :key="message.id"
              class="message-wrapper"
              :class="[message.type, index]"
            >
            <!-- Chat Profile Icon of Walfred -->
            <FishBadge v-if="message.type != 'answer'" class="walfred-spricht" />

              <!-- Walfreds Chatbubble-->
              <transition appear name="slide-from-left">
                <div
                  v-if="message.type != 'answer'"
                  class="chat-bubble"
                  :class="showTypingIfActiveSlide(index) && 'is-typing'" 
                >
                  <!-- Typing Indicator, used to create a nice looking typing effect -->
                  <label
                    v-if="showTypingIfActiveSlide(index)"
                    class="typing-bubble"
                  >
                    <span>.</span>
                    <span>.</span>
                    <span>.</span>
                  </label>

                  <SlideText
                    v-if="!showTypingIfActiveSlide(index) && message.type == 'text'"
                    :slide="message"
                  />
                </div>
              </transition>

              <!-- User Answer Chatbubble-->
              <transition appear name="slide-from-right">
                <div v-if="message.type == 'answer'" class="chat-bubble">
                  <UserAnswer
                    v-if="message.type == 'answer'"
                    :slide="message"
                  />
                </div>
              </transition>
              

              <!-- Walfred asks the User something -> container with possible User Answers appears -->
              <teleport
                v-if="
                  (message.answers &&
                    showAnswerContainer &&
                    index == conversation.length - 1) ||
                  (showEndOnboarding && index == conversation.length - 1)
                "
                :to="teleportTo"
              >
                <transition appear name="slide-from-bottom">
                  <div class="wave-bottom-container">
                    <!-- Wave Animation -->
                    <div class="wave-container">
                      <div class="water"><Wave /></div>
                      <div class="water"><Wave /></div>
                    </div>

                    <!-- Buttons with possible User Answers-->
                    <div class="btn-area" v-if="!showEndOnboarding">
                      <button
                        v-for="answer in message.answers"
                        :key="answer.keyword"
                        class="message-wrapper answer"
                        :class="[isSelected(answer) ? 'orange' : 'secondary']"
                        @click="handleButtonClick(answer)"
                      >
                      	<div class="chat-bubble">
                          <label>{{ answer.text }}</label>
                        </div>
                      </button>
                    </div>

                    <!-- Select User Answer Button -->
                    <button
                      v-if="!showEndOnboarding && selectedAnswers.length > 0"
                      class="btn wave primary"
                      @click="saveReminders"
                    >
                      <IconArrow />
                    </button>

                    <!-- Skip Onboarding Button -->
                    <div v-if="message.skip && selectedAnswers.length == 0">
                      <button
                        v-for="(skip, index) in message.skip"
                        :key="index"
                        class="btn textlink"
                        @click="skipReminders(skip)"
                      >
                        <label>{{ skip.text }}</label>
                      </button>
                    </div>

                    <!-- End of Onboarding -> Start Guide Button -->
                    <button
                      v-if="showEndOnboarding"
                      class="btn primary"
                      @click="endOnboarding"
                    >
                      Guide starten
                    </button>

                  </div>
                </transition>
              </teleport>
            </div>
          </div>
        </section>
      </div>
    </div>
  </div>
</template>

<script>
import "@lottiefiles/lottie-player";
import reject from "lodash/reject";
import random from "lodash/random";
import find from "lodash/find";
import forEach from "lodash/forEach";
import isEqual from "lodash/isEqual";
import remove from "lodash/remove";

import SlideText from "./components/SlideText.vue";
import UserAnswer from "./components/UserAnswer.vue";

import Wave from "../public/assets/svg/wave.svg";
import IconArrow from "../public/assets/svg/icon-arrow.svg";
import Fish from "../public/assets/svg/fish.svg";
import FishBadge from "../public/assets/svg/fish-badge.svg";

import jsonConversation from "./jsonConversation.json";

export default {
  components: {
    SlideText,
    UserAnswer,
    Fish,
    FishBadge,
    Wave,
    IconArrow,
  },
  props: {
  },
  data() {
    return {
      showEndOnboarding: false,
      conversation: [],
      delayBot: {
        min: 1000,
        max: 1600,
      },
      showIsTyping: true,
      showAnswerContainer: true,
      teleportTo: ".chat-wrapper",
      nextSlideTimeout: 500,
      selectedAnswers: [],
      showSubmitAnswerBtn: false,
      remindersForTopics: [],
      needsAccesibility: false,
      onboardingIsCompleted: false,
    };
  },
  computed: {
    // get only Walfred Messages from current conversation (no user replies)
    walfredMessages() {
      let messages = reject(this.conversation, { type: "answer" });
      return messages;
    },
  },
  mounted() {
    const vm = this;
    //open first Walfred message
    vm.nextMessage();
  },
  methods: {
    // Chat Bot writes the next message
    nextMessage() {
      const vm = this;
      // reset Select-User-Answer Container
      this.showAnswerContainer = null;

      // Check if next message exists
      if (jsonConversation[this.walfredMessages.length] == undefined) {
        vm.showEndOnboarding = true; //show End Tour Button
        //Scroll to User Answer
        vm.scrollAnimation();
        return;
      }

      let currentMessage;

      // Case Default -> Get Next Message and push it in conversation
      currentMessage = jsonConversation[this.walfredMessages.length];
      this.conversation.push(currentMessage);

      //set showIsTyping for neat typing effect
      vm.showIsTyping = true;

      //Scroll to User Answer
      vm.scrollAnimation();

      // wait a bit, then show the message and init stuff
      let nextMessageTimout = setTimeout(function () {
        // show the message / hide the typing bubbles
        vm.showIsTyping = false;

        // inject answer if it exists
        if (currentMessage.answers) {
          vm.userAnswers();
        } else {
          //scroll to bottom of div
          let chatBoxInner = document.querySelector("#messages");
          if (chatBoxInner)
            chatBoxInner.scrollTop =
              chatBoxInner.scrollHeight - chatBoxInner.clientHeight;
          // store progress
          vm.nextMessage();
        }

        //Scroll to User Answer
        vm.scrollAnimation();
      }, random(vm.delayBot.min, vm.delayBot.max));
    },

    // Save which Reminders User wants to subscribe to
    saveReminders() {
      const vm = this;

      let userAnswerText = "";
      let separator = "";

      forEach(this.selectedAnswers, function (answer) {
        if (userAnswerText == "") separator = "";
        else separator = ", ";
        //Get String for selected User Answer
        userAnswerText = userAnswerText + separator + answer.text;
        //... and save it  -> this can be later used f.i. for push notifications
        vm.remindersForTopics.push(answer.keyword);
      });

      vm.handleUserAnswer(userAnswerText);
    },
    skipReminders(answer) {
      const vm = this;
      vm.handleUserAnswer(answer.text);
    },
    handleUserAnswer(text) {
      const vm = this;

      //wait a Bit for more natural timing -> then insert user-answer
      setTimeout(() => {
        //push user answer in chat dialog
        let userAnswer = {};
        userAnswer.text = text;
        userAnswer.type = "answer";
        this.conversation.push(userAnswer);

        //hide Answer Container
        this.showAnswerContainer = false;
        //scrollanimation
        this.scrollAnimation();

        //Continue with Next Message
        setTimeout(() => {
          this.nextMessage();
        }, vm.nextSlideTimeout);
      }, vm.nextSlideTimeout);
    },
    //Case: User chooses one of the answers
    saveAccessibilityResponse(answer) {
      const vm = this;
      //Question Accessibility -> save User response
      this.needsAccesibility = answer.keyword;
      //hide Answer Container
      vm.handleUserAnswer(answer.text);
    },
    scrollAnimation() {
      //Scroll to User Answer
      setTimeout(() => {
        if (document.querySelector(".wave-bottom-container") != undefined) {
          let userAnswerHeight = document.querySelector(
            ".wave-bottom-container"
          ).offsetHeight;
          document.getElementById("messages").style.paddingBottom =
            userAnswerHeight + "px";
        }

        //Timing -> wait a bit before doing this
        setTimeout(() => {
          //scroll to bottom of div
          let chatBoxInner = document.querySelector("#messages");
          if (chatBoxInner)
            chatBoxInner.scrollTop =
              chatBoxInner.scrollHeight - chatBoxInner.clientHeight;
        }, 150);
      }, 1);
    },
    showTypingIfActiveSlide(index) {
      if (this.showIsTyping && index == this.conversation.length - 1)
        return true;
      else return false;
    },
    userAnswers() {
      const vm = this;
      //show AnswerContainer
      vm.showAnswerContainer = true;
      this.scrollAnimation();
    },
    endOnboarding() {
      //Finish Onboarding
      this.onboardingIsCompleted = true;
      //DONE -> do your custom logic here
      console.log("done");
    },

    handleButtonClick(answer) {
      if (answer.question == "accessibility")
        this.saveAccessibilityResponse(answer);
      else this.selectAnswer(answer);
    },
    selectAnswer: function (answer) {
      const vm = this;

      if (this.selectedAnswers.includes(answer)) {
        vm.selectedAnswers = remove(vm.selectedAnswers, function (n) {
          return n != answer;
        });
      } else {
        this.selectedAnswers.push(answer);
      }

      // Show / Hide Submit Btn
      if (this.selectedAnswers.length > 0) this.showSubmitAnswerBtn = true;
      else this.showSubmitAnswerBtn = false;
    },

    isSelected: function (answer) {
      const vm = this;
      let isSelected = false;
      let selected = find(vm.selectedAnswers, function (obj) {
        if (isEqual(answer, obj)) return obj;
      });
      if (selected !== undefined) isSelected = true;
      return isSelected;
    },
  },
};
</script>

<!-- Import Stylesheet -->
<style lang="less">
@import "./styles/styles.less";
</style>