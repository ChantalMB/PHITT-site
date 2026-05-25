<script lang="ts">
	import ArrowCounterClockwiseIcon from 'phosphor-svelte/lib/ArrowCounterClockwiseIcon';
    import FrameCornersIcon from 'phosphor-svelte/lib/FrameCornersIcon';

	import { onMount } from 'svelte';
	import { page } from '$app/state';

	import { ImageViewer } from 'svelte-image-viewer';

	let imageViewer = $state<ImageViewer>();

	let imageViewerOffsetXTarget = $state(0);
	let imageViewerOffsetYTarget = $state(0);
	let imageViewerScaleTarget = $state(1.0);

	let data: any = $state(null);

	const skipKeys = new Set([
		'ID',
		'image_name',
		'image_permalink',
		'topic',
		'country',
		'region',
		'subregion'
	]);

	const formatKey = (k: string) =>
		k.replace(/_/g, ' ').replace(/\b\w/g, (c) => c.toUpperCase());

	const isUrl = (v: unknown) => typeof v === 'string' && /^https?:\/\//.test(v);
	
    let detailEntries = $derived(
		data
			? Object.entries(data).filter(([k, v]) => !skipKeys.has(k) && v !== null && v !== '')
			: []
	);

	onMount(async () => {
		const response = await fetch(
			'https://phitt-database.fly.dev/phitt/phitt_data.json?_shape=array&_sort=ID&ID__exact=' +
				page.params.id
		);
		const results = await response.json();
		data = results[0] ?? null;
		console.log(data);
	});

    function fitImageViewer() {
        imageViewer?.scaleImageToFit();
    }

	function resetImageViewer() {
		imageViewerOffsetXTarget = 0;
		imageViewerOffsetYTarget = 0;
		imageViewerScaleTarget = 1;
	}
</script>

<div class="h-[calc(100dvh-4rem)] lg:h-screen">
	<div class="grid h-full grid-cols-6 grid-rows-6 gap-4">
		<div class="relative col-span-6 row-span-3 h-full lg:col-span-3 lg:row-span-6">
            <div class="absolute top-3 left-3 z-10 flex gap-2">
                <button
                    type="button"
                    onclick={fitImageViewer}
                    class="flex items-center justify-center rounded-md bg-paper/50 p-2 text-white backdrop-blur-sm transition hover:bg-paper/70"
                >
                    <FrameCornersIcon weight="duotone" size={20} />
                </button>
                <button
                    type="button"
                    onclick={resetImageViewer}
                    class="flex items-center justify-center rounded-md bg-paper/50 p-2 text-white backdrop-blur-sm transition hover:bg-paper/70"
                >
                    <ArrowCounterClockwiseIcon weight="duotone" size={20} />
                </button>
            </div>
			<ImageViewer
				bind:this={imageViewer}
				bind:targetOffsetX={imageViewerOffsetXTarget}
				bind:targetOffsetY={imageViewerOffsetYTarget}
				bind:targetScale={imageViewerScaleTarget}
				src={data?.image_permalink}
			/>
		</div>
		<div class="col-span-6 row-span-3 row-start-4 lg:col-span-3 lg:col-start-4 lg:row-span-6 overflow-y-auto">
			<div class="h-full p-6 lg:p-10 font-Inter text-[#2b2620]">
                {#if data}
                    <div class="inline-flex items-center gap-2 text-xs uppercase tracking-[0.18em] font-medium text-[#bfa980]">
                        <span class="inline-block h-1.5 w-1.5 rounded-full bg-orange-700"></span>
                        <span>{data.region}{data.subregion ? ` · ${data.subregion}` : ''}</span>
                    </div>

                    <h1 class="font-fraunces font-black leading-[1.05] tracking-[-0.02em] text-paper mt-3 mb-1 text-[clamp(1.75rem,3.2vw,2.5rem)]">
                        {data.topic ?? 'Untitled'}
                    </h1>
                    <p class="font-fraunces italic font-medium text-[#d8c5a3] m-0 text-[clamp(1rem,1.6vw,1.15rem)]">
                        {data.country}
                    </p>

                    <div aria-hidden="true" class="h-px my-5 bg-[linear-gradient(to_right,rgba(255,255,255,0.25),transparent)]"></div>

                    <dl class="text-base leading-[1.55] text-[#e9dcc4] m-0 grid grid-cols-1 lg:grid-cols-[max-content_1fr] gap-x-6 gap-y-3">
                        {#each detailEntries as [key, value]}
                            <dt class="text-xs uppercase tracking-[0.16em] font-semibold text-[#bfa980] lg:self-start lg:pt-0.5">
                                {formatKey(key)}
                            </dt>
                            <dd class="m-0 wrap-break-word min-w-0 text-paper">
                                {#if isUrl(value)}
                                    <a
                                        href={value as string}
                                        target="_blank"
                                        rel="noopener noreferrer"
                                        class="underline decoration-dotted underline-offset-2 text-[#f0c98a] hover:text-orange-300 break-all"
                                    >
                                        {value}
                                    </a>
                                {:else}
                                    {value}
                                {/if}
                            </dd>
                        {/each}
                    </dl>
                {/if}
            </div>
		</div>
	</div>
</div>
