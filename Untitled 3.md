```
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

```