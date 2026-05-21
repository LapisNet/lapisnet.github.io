<script setup>
import { ref, onBeforeMount } from 'vue';
import { fetchData } from '../libs/fetch-data';

const conctacts = ref([]);
const links = ref([]);

onBeforeMount(async() => {
	conctacts.value = await fetchData('contacts');
	links.value = await fetchData('links');
});
</script>

<template>
	<div id="footer">
		<div class="content">
			<img class="banner" title="LapisNet" src="/res/banner.webp" />
			<p>Copyright (c) 2025 LapisNet. All rights reserved.</p>
		</div>
		<div class="content" data-title="我们用到的技术">
			<a href="https://vuejs.org" target="_blank" rel="noopener noreferrer">Vue.js</a>
			<a href="https://picocss.com" target="_blank" rel="noopener noreferrer">Pico CSS</a>
			<a href="https://fontawesome.com" target="_blank" rel="noopener noreferrer">Font Awesome</a>
			<a href="https://sass-lang.com" target="_blank" rel="noopener noreferrer">Sass</a>
		</div>
		<div class="content" data-title="联系我们">
			<span class="contact-item" v-for="conctact of conctacts" :key="conctact.icon">
				<i :class="conctact.icon"></i>
				<a :href="conctact.url" :target="conctact === conctacts['email']? '': '_blank'" rel="noopener">{{ conctact.title || conctact.id }}</a>
			</span>
		</div>
		<div class="content" data-title="友情链接">
			<a v-for="link of links" :href="link.link" target="_blank" rel="noopener">{{ link.title }}</a>
		</div>
	</div>
</template>

<style scoped lang="scss">
#footer {
	padding: 1em 0.4em;
	display: grid;
	align-items: start;
	justify-content: space-between;
	grid-template-columns: 22.6em 15% 15% 15%;
	font-size: 16px;
	height: fit-content;
	transition: opacity 0.3s ease-in-out;
	&.scrolled {
		opacity: 0;
		pointer-events: none;
	}
}
.content {
	display: flex;
	flex-direction: column;
	&::before {
		content: attr(data-title);
		font-size: 18px;
		font-weight: bold;
	}
}
p {
	margin: 0;
	font-size: 0.8em;
	text-align: center;
	margin-top: 0.2em;
}
.banner {
	filter: drop-shadow(2px 2px 2px rgba(0,0,0,0.2));
}
@media screen and (max-width: 768px) {
	.content:not(:first-child), .banner {
		display: none;
	}
}
</style>

<script>
export default {
	name: 'Footer'
}
</script>
