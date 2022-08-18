<template>
	<div>	
		<article
			v-if="slide.text"
			v-html="slide.text"
		></article>
	</div>
</template>

<script>


export default {
	components: {
	},
	props: {
        slide: {},
	},
	data: function () {
		return {};
	},
	computed: {
	},
	mounted() {
    },

	methods: {
	},
};
</script>
