<script lang="ts">
	import { onMount } from 'svelte';
	import filters from '$lib/assets/filters.json';

	let data: any[] = $state([]);

	onMount(async () => {
		const response = await fetch(
			'https://phitt-database.fly.dev/phitt/phitt_data.json?_shape=array&_size=max'
		);
		data = await response.json();
	});

	let sort: Array<string> = $state(['ID', 'Date (First Stamp)', 'Date (First Historical Stamp)']);
    $effect(() => {
		console.log('Selection changed to:', sortSelection);
	});
	
    let sortSelection: string = $state('ID');

    let categories: Array<string> = $state([
		'All',
		'Country',
		'Subregion',
		'Region',
		'Theme',
		'Topic'
	]);

	const categoryToFilterKey: Record<string, keyof typeof filters> = {
		Country: 'countries',
		Subregion: 'subregion',
		Region: 'region',
		Theme: 'themes',
		Topic: 'topics'
	};

	let selectedCategory: string = $state('All');
	let selectedValue: string = $state('');
	let topicSearch: string = $state('');

	let filterOptions = $derived(
		selectedCategory in categoryToFilterKey
			? (filters[categoryToFilterKey[selectedCategory]] as string[])
			: []
	);

	function applyFilters() {
		const value = selectedCategory === 'Topic' ? topicSearch : selectedValue;
		console.log('Applying filter:', { category: selectedCategory, value });
		// TODO: implement filtering of `data`
	}
</script>

<div class="bg-[#585936]">
	<div class="fixed top-0 right-0 left-0 z-50 bg-paper shadow">
		<div
			class="flex flex-col gap-3 px-3 py-2 font-inter sm:h-20 sm:flex-row sm:items-end sm:justify-between sm:gap-6 sm:px-6 sm:py-3"
		>
			<div class="flex min-w-0 items-end gap-2 sm:gap-3">
				<label
					class="flex min-w-0 flex-col gap-1 text-xs tracking-wide text-[#585936]/80 uppercase"
				>
					<span>Filter</span>
					<select
						bind:value={selectedCategory}
						class="filter-control w-full min-w-0 cursor-pointer pr-7 sm:w-auto sm:min-w-32 sm:pr-8"
					>
						{#each categories as category}
							<option value={category}>{category}</option>
						{/each}
					</select>
				</label>

				{#if selectedCategory === 'Topic'}
					<input
						type="search"
						list="topic-options"
						bind:value={topicSearch}
						placeholder="Search topics…"
						class="filter-control min-w-0 flex-1 sm:min-w-56"
					/>
					<datalist id="topic-options">
						{#each filters.topics as topic}
							<option value={topic}></option>
						{/each}
					</datalist>
				{:else}
					<select
						bind:value={selectedValue}
						disabled={selectedCategory === 'All'}
						class="filter-control min-w-0 flex-1 cursor-pointer pr-7 disabled:cursor-not-allowed disabled:opacity-50 sm:min-w-56 sm:pr-8"
					>
						<option value=""
							>{selectedCategory === 'All'
								? 'Select a filter category…'
								: `Select ${selectedCategory}…`}</option
						>
						{#each filterOptions as option}
							<option value={option}>{option}</option>
						{/each}
					</select>
				{/if}

				<button type="button" onclick={applyFilters} class="filter-apply shrink-0">
					Apply
				</button>
			</div>

			<div class="flex min-w-0 items-end gap-2 sm:gap-3">
				<label
					class="flex min-w-0 flex-col gap-1 text-xs tracking-wide text-[#585936]/80 uppercase sm:w-auto"
				>
					<span>Sort</span>
					<select
						bind:value={sortSelection}
						class="filter-control w-full min-w-0 cursor-pointer pr-7 sm:w-auto sm:min-w-40 sm:pr-8"
					>
						{#each sort as s}
							<option value={s}>{s}</option>
						{/each}
					</select>
				</label>
			</div>
		</div>
		<div
			aria-hidden="true"
			class="pointer-events-none absolute right-0 bottom-0 left-0 h-[2px] bg-[linear-gradient(to_right,rgba(0,0,0,0.35)_0_6px,transparent_6px_12px)]
                bg-size-[12px_2px]
                opacity-50"
		></div>
	</div>

	<div class="pt-36 pb-16 sm:pt-20">
		<!-- adapted from: https://codepen.io/Everybodyknows/pen/XWJRmKm -->
		<div class="columns-2 gap-3 p-3 lg:columns-3 xl:columns-4">
			{#each data as stamp}
				<div class="group stamp -m-px block w-full break-inside-avoid">
					<div class="stamp-paper">
						<a href="/explore/{stamp.ID}" class="relative overflow-hidden">
							<img src={stamp.image_permalink} alt="" loading="lazy" class="block h-auto w-full" />
							<div
								class="absolute inset-0 flex items-end bg-linear-to-t from-black/60 to-transparent px-2 py-2 opacity-0 transition-opacity duration-200 group-hover:opacity-100"
							>
								<span
									class="line-clamp-3 p-1.5 font-fraunces text-base leading-tight font-medium text-white italic"
									>{stamp.topic}</span
								>
							</div>
						</a>
					</div>
				</div>
			{/each}
		</div>
	</div>
</div>
