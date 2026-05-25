<script lang="ts">
    import { goto } from '$app/navigation';
    import coverImages from '$lib/assets/cover-images.json';

    const ROWS = 5;
    const PER_ROW = 6;
    const pool = coverImages.stampImages;
    const shuffled = [...pool].sort(() => Math.random() - 0.5);
    const total = Math.min(ROWS * PER_ROW, shuffled.length);
    const perRow = Math.floor(total / ROWS);
    const rows: string[][] = Array.from({ length: ROWS }, (_, i) =>
        shuffled.slice(i * perRow, (i + 1) * perRow)
    );

    function extractStampId(url: string): string | null {
        const filename = url.split('/').pop() ?? '';
        const match = filename.match(/^(\d+)-/);
        return match ? match[1] : null;
    }

    function handleStampClick(event: MouseEvent, img: string) {
        const id = extractStampId(img);
        if (!id) return;
        event.preventDefault();
        goto(`/explore/${id}`);
    }
</script>

<!-- adapted from: https://blog.logto.io/css-only-infinite-scroll -->
<div class="carousel-grid grid h-screen grid-cols-5 grid-rows-5 gap-[10px] bg-[#585936]">
    {#each { length: 5 } as _, i}
	<div class="carousel col-span-5 mx-auto flex w-screen h-full overflow-hidden">
		<div class="{i % 2 === 0 ? 'scroller' : 'scroller-reverse'} flex h-full min-h-0 shrink-0 basis-[200%] sm:basis-[150%] md:basis-full gap-5 px-[10px] will-change-transform" style="animation-delay: {i * -7}s">
			{#each rows[i] as img}
            <a
				href="/explore/{extractStampId(img)}"
				onclick={(e) => handleStampClick(e, img)}
				class="card flex h-full min-h-0 w-full flex-col items-center justify-center opacity-65 hover:opacity-100"
			>
				<img
					class="block max-h-full w-auto max-w-full object-contain"
					src={img}
					alt="Welcome"
				/>
			</a>
            {/each}
		</div>
		<div aria-hidden class="{i % 2 === 0 ? 'scroller' : 'scroller-reverse'} flex h-full min-h-0 shrink-0 basis-[200%] sm:basis-[150%] md:basis-full gap-5 px-[10px] will-change-transform" style="animation-delay: {i * -7}s">
			{#each rows[i] as img}
            <a
				href="/explore/{extractStampId(img)}"
				tabindex="-1"
				class="card flex h-full min-h-0 w-full flex-col items-center justify-center opacity-50 hover:opacity-100"
			>
				<img
					class="block max-h-full w-auto max-w-full object-contain"
					src={img}
					alt="Welcome"
				/>
			</a>
            {/each}
		</div>
	</div>
    {/each}
</div>

<style>
	/* TODO: should this be in layout.css too? */
	.scroller {
		animation: scrolling 60s linear infinite;
	}
	.scroller-reverse {
		animation: scrolling-reverse 60s linear infinite;
	}
	@keyframes scrolling {
		0% {
			transform: translateX(0);
		}
		100% {
			transform: translateX(-100%);
		}
	}
	@keyframes scrolling-reverse {
		0% {
			transform: translateX(-100%);
		}
		100% {
			transform: translateX(0);
		}
	}
</style>
