<script lang="ts">
	import 'highlight.js/styles/default.min.css'
	import type { Post } from '$routes/data/posts.json/+server'

	import { t } from '$lib/translations'
	import Placeholder from './placeholder.png'
	import { onMount } from 'svelte'
	export let post: Post & { content: string }
	$: tags = post.attributes.tags.filter((t) => !t.startsWith('c_'))
	export let show = false
	export let article = false
	let btn: HTMLElement
	onMount(() => {
		// console.log(btn)
		btn.addEventListener('show.bs.collapse', () => {
			show = true
		})
		btn.addEventListener('hide.bs.collapse', () => {
			show = false
		})
	})
	$: banner = post.attributes?.banner ?? Placeholder
	import { toc } from './toc'
	export let parent: string = ''
</script>

<div class="card rounded-4">
	<div class="card-body">
		<h4 class="card-title">
			<a href={post.path} class="nav-link active">
				{post.attributes.title}
			</a>
		</h4>
		{#if tags.length}
			<div class="row row-cols-auto g-2">
				{#each tags as tag}
					<div class="col">
						<button class="btn btn-sm rounded-5 btn-light" disabled>{tag}</button>
					</div>
				{/each}
			</div>
		{/if}
		{#if post.attributes.desc}
			<p class="card-text">
				{post.attributes.desc}
			</p>
		{/if}
	</div>
	<div class="collapse" class:show id="post-{post.path}" bind:this={btn} data-bs-parent={parent}>
		<div class="card-body border-top markdown-body" use:toc>
			{@html post.content}
		</div>
	</div>
	{#if !article}
		<a
			class="card-footer text-center"
			class:active={show}
			data-bs-toggle="collapse"
			href="#post-{post.path}"
		>
			<small>
				{show ? $t('common.collapse_artcile') : $t('common.expand_artcile')}
			</small>
		</a>
	{/if}
</div>

<style>
	a.card-footer {
		text-decoration: none;
		color: #6b7280;
		transition: all 0.3s;
	}
	a.card-footer:hover,
	a.card-footer:active,
	a.card-footer.active {
		color: #000;
		background: #f3f4f6;
		box-shadow: inset 0px 1px 0px #0000001a;
	}
	.card {
		overflow: hidden;
	}
	.card-body :global(img) {
		max-width: 100%;
	}
	.card-body {
		padding: 2em;
	}
	.card-text {
		margin-top: 10px;
	}
	.card :global(.table-of-contents) {
		position: fixed;
		top: 6rem;
		right: calc(50% + 1rem);
		z-index: 9;
		box-shadow: 0px 2px 6px rgba(0, 0, 0, 0.1);
		margin-bottom: 0;
		width: 240px;
	}
</style>
