<script lang="ts">
	import { onMount, tick } from 'svelte';
	import filters from '$lib/assets/filters.json';
	import MailboxIcon from 'phosphor-svelte/lib/MailboxIcon';


	let isLoading: boolean = $state(false)
	let imagesReady: boolean = $state(false)
	let data: any[] = $state([]);

	let sort: Record<string, string> = $state({'ID': 'ID', 'Date (First Stamp)': 'date_of_first_stamp', 'Date (First Historical Stamp)': 'date_of_first_historical_stamp'});
    $effect(() => {
		console.log('Selection changed to:', sortSelection);
		loadData();
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

	const categoryToFilter: Record<string, keyof typeof filters> = {
		Country: 'country',
		Subregion: 'subregion',
		Region: 'region',
		Theme: 'theme',
		Topic: 'topic'
	};

	let selectedCategory: string = $state('All');
	let selectedValue: string = $state('');
	let topicSearch: string = $state('');

	let appliedFilter: { key: string; value: string } | null = $state(null);

	let filterOptions = $derived(
		selectedCategory in categoryToFilter
			? (filters[categoryToFilter[selectedCategory]] as string[])
			: []
	);

	async function loadData() {
		isLoading = true;
		imagesReady = false;
		const params = new URLSearchParams({
			_shape: 'array',
			_size: 'max',
			_sort: sort[sortSelection]
		});

		if (appliedFilter) {
			params.set(appliedFilter.key, appliedFilter.value);
		}

		try {
			const response = await fetch(
				`https://phitt-database.fly.dev/phitt/phitt_data.json?${params.toString()}`
			);
			const json = await response.json();
			
			// prevent visible masonry reflow by preloading images
			await preloadImages(json.map((d: any) => d.image_permalink));
			data = json;
			await tick();
			imagesReady = true;
		} finally {
			isLoading = false;
		}
	}

	function preloadImages(urls: string[]): Promise<void> {
		if (!urls.length) return Promise.resolve();
		return new Promise((resolve) => {
			let remaining = urls.length;
			const done = () => { if (--remaining <= 0) resolve(); };
			for (const url of urls) {
				if (!url) { done(); continue; }
				const img = new Image();
				img.onload = done;
				img.onerror = done;
				img.src = url;
			}
		});
	}

	async function applyFilters() {
		if (selectedCategory === 'All') {
			appliedFilter = null;
		} else {
			const value = selectedCategory === 'Topic' ? topicSearch : selectedValue;
			if (!value) return;
			appliedFilter = {
				key: `${categoryToFilter[selectedCategory]}__contains`,
				value
			};
		}
		loadData();
	}
</script>

<div class="bg-[#585936] min-h-screen">
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
						{#each filters.topic as topic}
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
						{#each Object.keys(sort) as s}
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

	<div class="relative pt-36 pb-16 sm:pt-20">
		{#if !imagesReady}
			<div
				class="flex min-h-screen flex-col items-center justify-center gap-3 bg-[#585936] text-[#f5f1e6]"
				aria-busy="true"
				aria-label="Loading stamps"
			>
				<MailboxIcon size={48} weight="bold" class="loading-icon" aria-hidden="true" />
				<span class="font-fraunces text-lg italic">Loading stamps…</span>
			</div>
		{/if}

		<!-- adapted from: https://codepen.io/Everybodyknows/pen/XWJRmKm -->
		<div
			class="columns-2 gap-3 p-3 transition-opacity duration-300 lg:columns-3 xl:columns-4"
			class:opacity-0={!imagesReady}
		>
			{#each data as stamp}
				<div class="group stamp -m-px block w-full break-inside-avoid">
					<div class="stamp-paper">
						<a href="/explore/{stamp.ID}" target="_blank" class="relative overflow-hidden">
							<img src={stamp.image_permalink} alt="" loading="lazy" class="block h-auto w-full" />
							<div
								class="absolute inset-0 flex items-end bg-linear-to-t from-black/60 to-transparent px-2 py-2 opacity-0 transition-opacity duration-200 group-hover:opacity-100"
							>
								{#if sort[sortSelection] == 'ID'}
								<span
									class="absolute top-2 right-2 rounded-full bg-black/60 px-2 py-0.5 font-fraunces text-xs font-semibold text-white shadow-sm backdrop-blur-sm"
									>{stamp.ID}</span
								>
								{:else if sort[sortSelection] == 'date_of_first_stamp'}
								<span
									class="absolute top-2 right-2 rounded-full bg-black/60 px-2 py-0.5 font-fraunces text-xs font-semibold text-white shadow-sm backdrop-blur-sm"
									>{stamp.date_of_first_stamp}</span
								>
								{:else if sort[sortSelection] == 'date_of_first_historical_stamp'}
								<span
									class="absolute top-2 right-2 rounded-full bg-black/60 px-2 py-0.5 font-fraunces text-xs font-semibold text-white shadow-sm backdrop-blur-sm"
									>{stamp.date_of_first_historical_stamp}</span
								>
								{/if}
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
