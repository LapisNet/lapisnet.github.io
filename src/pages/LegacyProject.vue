<script setup>
import {ref, onBeforeMount} from 'vue';
import { fetchData } from '../libs/fetch-data';
import { parseRawText } from '../libs/late-marked';

/** @type {Project[]} */
const projects = ref([]);
const showedProjectCount = ref(0);
onBeforeMount(async() => {
	projects.value = await fetchData('projects');
	projects.value.forEach(pj => (typeof pj._show === 'undefined' || pj._show) && showedProjectCount.value++);
});
</script>

<template>
	<div id="main">
		<div class="title">项目列表</div>
		<div class="content">
			<p class="loading" v-if="projects.length === 0">正在加载...</p>
			<template v-else>
				<div id="pj-ls" :style="`grid-template-rows: repeat(${Math.ceil(showedProjectCount / 2)}, 1fr);`">
					<template v-for="item in projects" :key="item.title">
						<div class="card card-project" v-if="typeof item._show === 'undefined' || item._show">
							<img class="pj-icon" :src="item.icon" :alt="item.title" v-if="!!item.icon" />
							<span class="pj-name">
								<div class="pj-title">{{item.title}}</div>
								<div class="pj-subtitle" v-if="item.sub_title" v-html="parseRawText(item?.sub_title)"></div>
								<status-badge :type="item._status" />
							</span>
							<div class="pj-desc">
								<div class="info1" v-html="parseRawText(item?.info1 ?? '/未提供信息/')" :style="item.info1? {}: { color: 'grey' }"></div>
								<div class="info2" v-if="item.info2" v-html="parseRawText(item?.info2)"></div>
							</div>
							<div class="pj-link">
								<a v-if="item.repo" :class="`fab fa-${(item?.repo_type ?? 'github').split('.')[0]}`" :href="`https://${item.repo_type ?? 'github.com'}/${item.repo}`" target="_blank" rel="noopener noreferrer">访问仓库</a>
								<a v-if="item.url" :href="item.url" target="_blank" rel="noopener">访问链接</a>
							</div>
						</div>
					</template>
				</div>
				<p class="no-more">没有更多项目了...</p>
			</template>
		</div>
	</div>
</template>

<style lang="scss" scoped>
#pj-ls {
	display: grid;
	align-items: center;
	grid-template-columns: repeat(2, 1fr);
	& .card-project {
		height: fit-content;
		// width: fit-content;
	}
}
.pj-name {
	display: inline-flex;
	align-items: baseline;
	& .pj-subtitle {
		margin-left: 0.4em;
	}
}
.info1::before {
	content: '- ';
}
@media screen and (max-width: 768px) {
	#pj-ls {
		grid-template-rows: repeat(10, auto);
		grid-template-columns: 1fr;
		& .card-project {
			height: 90%;
		}
	}
}
</style>
