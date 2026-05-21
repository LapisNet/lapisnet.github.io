<script setup>
import { ref, onMounted } from 'vue';
import { pages } from '../router';
import { fetchData } from '../libs/fetch-data';

const showMenu = ref(false);
const links = ref([]);

/** @param {HTMLElement} target */
const closeMenu = (target) => {
	target && target.setAttribute('data-show', 'false');
	setTimeout(() => showMenu.value = false, 350);
};
const openMenu = () => showMenu.value = true;

const time = ref('xx:xx xx');
onMounted(async() => {
	/** 初始化时钟 */
	setInterval(() => {
		const now = new Date();
		const hour = now.getHours() < 10? `0${now.getHours()}`: now.getHours();
		const minute = now.getMinutes() < 10? `0${now.getMinutes()}`: now.getMinutes();
		const period = hour < 12? 'AM': 'PM';
		time.value = `${hour}:${minute} ${period}`;
	}, 1000);

	links.value = await fetchData('links');
});
</script>

<template>
	<div id="nav">
		<div class="left">
			<router-link to="/" class="first"><span class="--lapis-icon"></span></router-link>

			<router-link to="/member" class="more">成员</router-link>
			<router-link to="/project" class="more">项目</router-link>
		</div>
		<div class="right">
			<router-link to="/about" class="more">关于</router-link>
			<span class="time">{{ time }}</span>
			<a class="menu" href="#menu" @click.prevent="openMenu"><i class="fa fa-bars"></i></a>
		</div>

		<div id="nav-menu" v-if="showMenu" @click.self="closeMenu($event.target)">
			<h2 class="menu-title">菜单</h2>
			<router-link class="menu-item" v-for="page in pages" :key="page.meta.name"
			 :to="page.path">{{ page.meta.name }}</router-link>
			<span class="separator" />
			<a class="menu-item" v-for="link in links" :key="link.title" :href="link.link"
				target="_blank" rel="noopener">{{ link.title }}</a>
		</div>
	</div>
</template>

<style scoped lang="scss">
@keyframes menu-slide-in {
	from { right: -40%; }
	to { right: 0; }
}
@keyframes menu-slide-out {
	from { right: 0; }
	to { right: -40%; }
}
a {
	text-decoration: none;
}
.--lapis-icon {
	&::before {
		content: '';
		display: inline-block;
		width: 32px;
		height: 32px;
		margin-bottom: -0.4em;
		background: url('/favicon.ico') no-repeat center;
		background-size: contain;
	}
	&::after {
		content: 'LapisNet';
		font-family: Minecraft, Unifont, system-ui, -apple-system, BlinkMacSystemFont;
		font-size: 1.2em;
		font-weight: bold;
	}
}
.left {
	width: 50%;
	display: inline-block;
	&>:not(.first) {
		margin-left: 1em;
	}
}
.right {
	width: 50%;
	display: inline-block;
	text-align: right;
	&>:not(:last-child) {
		margin-right: 1em;
	}
}
.time {
	font-family: Minecraft, Unifont, system-ui, -apple-system, BlinkMacSystemFont;
	font-style: normal;
	user-select: none;
	&:hover::before {
		transform: rotate3d(0,2,0,180deg);
	}
	&::before {
		content: '';
		display: inline-block;
		width: 1.2em;
		height: 1.2em;
		margin-bottom: -0.2em; // 对齐右侧时间文字
		background-image: url('/res/clock.png');
		background-size: contain;
		background-repeat: no-repeat;
		background-position: center;
		transition: transform 0.3s ease-in-out;
	}
}
.less, .menu {
	display: none;
}
.separator {
	display: block;
	height: 2px;
	background-color: color-mix(in srgb, var(--pico-contrast-border), transparent 50%);
	margin: 0.4em 0;
}
@media screen and (max-width: 768px) {
	.nav {
		font-size: 125%;
	}
	#nav-menu {
		&::before {
			content: '';
			display: block;
			position: fixed;
			top: 0;
			left: 0;
			width: 100vw;
			height: 100vh;
			backdrop-filter: blur(8px);
			background-color: rgba(0,0,0,0.4);
			z-index: -1;
		}
		display: flex;
		flex-direction: column;
		font-size: 115%;
		background-color: var(--pico-secondary-background);
		position: fixed;
		top: 0;
		right: -40%;
		height: 100vh;
		width: 40%;
		z-index: 10;
		animation: menu-slide-in 0.3s forwards ease-in-out;
		&[data-show="false"] {
			animation: menu-slide-out 0.3s forwards ease-in-out;
		}
		.menu-title {
			margin: 0.2em 0.4rem;
		}
		.menu-item {
			border-radius: 6px 0 0 6px;
			margin-left: 0.4rem;
			&.router-link-active {
				background-color: rgba(0, 122, 255, 0.2);
			}
		}
	}
	.right>*:not(:last-child) {
		margin-right: 0.4rem;
	}
	.menu {
		display: inline-block;
	}
	.more {
		display: none;
	}
	.--lapis-icon {
		margin-left: -12px;
		&::after {
			font-size: 1em;
		}
	}
}
</style>
