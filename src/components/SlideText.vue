<template>
	<div>
		<article
			v-if="slide.text"
			:class="slide.title ? '' : 'no_title'"
			v-html="slide.text"
		></article>
	</div>
</template>

<script>

export default {
	components: {
	},
	props: {
		slide: {
			type: Object,
			default: () => {
				return {};
			},
		}
	},
	emits: ['sliderGalleryWasClosed'],
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
