[```
<script lang="ts">
	import SearchBar from '../../lib/components/SearchBar/SearchBar.svelte';
	import ErrorMessage from '$lib/components/ItemList/ErrorMessage.svelte';
	import FilterButton from '$lib/components/Filters/FilterButton.svelte';
	import CloseButton from '$lib/components/Filters/CloseButton.svelte';
	import { projects, selectedTags } from '$lib/projects/projects';
	import ItemList from '$lib/components/ItemList/ItemList.svelte';
	import Filters from '$lib/components/Filters/Filters.svelte';
	import { crossfade, fade, fly } from 'svelte/transition';
	import { queryParam } from 'sveltekit-search-params';
	import BlogList from './components/BlogList.svelte';
	import Footer from '$lib/components/Footer.svelte';
	import Link from '$lib/components/UI/Link.svelte';
	import { browser } from '$app/environment';
	import { quadOut } from 'svelte/easing';
	import { onDestroy } from 'svelte';
	import { t } from '$lib/lang';

	let filtering: boolean = false;

	const includesSome = (arr: any[], values: any[]) => values.some((v) => arr.includes(v));

	let errorMessage: boolean = false;

	// Tags
	const checkIfNoMatch = () => {
		let count: number = 0;
		count = 0;
		if ($selectedTags.length > 0) {
			for (let pr of projects) {
				if (includesSome(pr.tags, $selectedTags)) {
					count = count + 1;
				}
			}
			if (count == 0) {
				errorMessage = true;
				return;
			}
		}
		errorMessage = false;
	};

	$: $selectedTags, checkIfNoMatch();

	const searchParam = queryParam('search');

	let initialSearchValue = $searchParam;
	let search: string | null = $searchParam;
	let searchHasChanged = false;

	$: searchHasChanged = search !== initialSearchValue;

	$: if ($searchParam === null || searchHasChanged) searchParam.set(search === '' ? null : search);

	onDestroy(() => {
		searchParam.set(null);
	});

	const [send, receive] = crossfade({
		duration: 200,
		fallback: (node) => fade(node),
	});
</script>

<title>Work - Enes Bala</title>

<!-- work -->
<div class="mx-auto mt-24 w-full font-display lg:w-10/12">
	<h1 class="headline mx-4 lg:mx-0">{$t('common.work')}</h1>
	<div class="relative mx-auto mt-24 text-xl lg:grid lg:grid-cols-10">
		<div class="mx-4 flex items-center justify-between lg:col-span-10 lg:mx-0 lg:mb-0 xl:col-start-2">
			<!-- titlebar -->
			<div class="col-span-2 col-start-2">
				<p class="font-semibold">{$t('common.allCategories')}</p>
			</div>
			<div class="col-span-6 col-start-5 row-start-2 flex cursor-pointer justify-end space-x-8 lg:cursor-none">
				<div class="flex items-center space-x-2">
					{#if search !== null}
						<div
							in:fly|local={{ x: 50, duration: 250 }}
							out:fly|local={{ opacity: 0, x: 50, duration: 250, easing: quadOut }}
							class="hidden lg:block"
						>
							<CloseButton onClick={() => (search = null)} />
						</div>
					{/if}
					<div class="z-10 rounded-full bg-neutral-50 dark:bg-black">
						<Link
							onClick={() => {
								search = '';
								$selectedTags = [];
								filtering = false;
								setTimeout(() => {
									if (browser) {
										const inputField = document?.getElementById?.('searchBarInputField');
										if (!inputField) return;
										inputField.focus({
											preventScroll: true,
										});
										// inputField.scrollIntoView({
										// 	behavior: 'smooth',
										// 	block: 'center',
										// 	// inline: 'center',
										// });
									}
								});
							}}
							text={$t('common.searchButtonPlaceholder')}
						/>
					</div>
				</div>
				<FilterButton bind:filtering searching={search !== null} />
			</div>
		</div>
		<SearchBar bind:search />

		{#if filtering && search === null}
			<Filters />
		{/if}
	</div>
	<!-- end titlebar -->
	<!-- project -->
	<ItemList>
		<BlogList {search} {projects} selectedTags={$selectedTags} />
		{#key errorMessage}
			{#if errorMessage}
				<ErrorMessage />
			{/if}
		{/key}
	</ItemList>
</div>
<!-- end contact -->

<!-- footer -->
<div class="mx-auto mt-24 font-display lg:mt-96">
	<Footer />
</div>

```](<%3Cscript lang="ts"%3E
	import { hoveredOverLink, notHovering } from '$lib/state/hoverOver.js';
	import Footer fro\m '$lib/components/Footer.svelte';
	import { formatDate } from '$lib/utils';
	import * as config from '$lib/config';

	export let data;
</script>

<svelte:head>
	<title>{config.title}</title>
</svelte:head>

<div class="mt-24 font-display lg:mt-24 container-base mb-64" on:mouseenter={notHovering}>
	<h1 class="headline">Blog</h1>
	<div class="relative mx-auto mt-20 pb-8 text-lg lg:grid lg:grid-cols-10 lg:gap-4">
		<div class="col-span-8 col-start-2 flex items-center justify-between" />

		{#each data.posts as post}
			<!-- <li class="post">
				<a href={post.slug} class="title">{post.title}</a>
				<p class="date">{formatDate(post.date)}</p>
				<p class="description">{post.description}</p>
			</li> -->
			<div class="col-span-8 col-start-2 text-base">
				<!-- <p class="opacity-70">Where I'm Located</p> -->
				<div
					class="group relative flex h-36 w-full items-center overflow-hidden rounded-2xl bg-neutral-200 dark:bg-darkgray lg:h-56"
				>
					<div class="absolute right-0 top-1/2 h-full w-2/3 -translate-y-1/2 overflow-hidden">
						<img
							src={post.image}
							alt={post.title}
							class="h-full w-full object-cover transition-all ease-in-out group-hover:scale-105"
						/>
					</div>
					<div
						class="absolute left-[33%] top-0 h-full w-full bg-gradient-to-r from-neutral-200 to-transparent dark:from-darkgray"
					/>
					<a
						href={post.slug}
						on:mouseenter={hoveredOverLink}
						on:mouseleave={notHovering}
						class="z-10 mx-8 flex w-1/2 flex-col"
					>
						<h3 class="projectH3 peer transition-all ease-in-out w-fit hover:underline duration-150 underline-offset-8">
							{post.title}
						</h3>
						<p class="peer-hover:opacity-90 transition-all duration-150 ease-in-out mt-3 description">
							{post.description}
						</p>
						<div class="mt-8 mb-2 w-72 h-px bg-gradient-to-r from-black to-transparent opacity-20 dark:from-white" />
						<p class="text-sm opacity-50">{formatDate(post.date)}</p>
					</a>
				</div>
			</div>
		{/each}
	</div>
</div>

<Footer />>)
```