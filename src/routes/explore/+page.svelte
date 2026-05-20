<script lang="ts">
	import { onMount } from 'svelte';

    let data: any[] = $state([])

    onMount(async () => {
		const response = await fetch("https://phitt-database.fly.dev/phitt/phitt_data.json?_shape=array&_size=max");
        data = await response.json();
        console.log(data)
	});
</script>

<div class="bg-amber-200">
    <div class="bg-white shadow fixed top-0 left-0 right-0 h-20 z-50">
        filters go here
    </div>
    <div class="pt-20 pb-16">
        <!-- adapted from: https://codepen.io/Everybodyknows/pen/XWJRmKm -->
        <div class="gap-3 p-3 columns-2 lg:columns-3 xl:columns-4">
            {#each data as stamp}
                <div class="stamp group block w-full break-inside-avoid -m-px">
                    <div class="stamp-paper">
                        <a href="/explore/{stamp.ID}" class="relative overflow-hidden">
                            <img
                                src={stamp.image_permalink}
                                alt=""
                                loading="lazy"
                                class="block w-full h-auto"
                            />
                            <div class="absolute inset-0 opacity-0 group-hover:opacity-100 bg-linear-to-t from-black/60 to-transparent px-2 py-2 transition-opacity duration-200 flex items-end">
                                <span class="p-1.5 text-white text-base font-fraunces italic font-medium leading-tight line-clamp-3">{ stamp.topic }</span>
                            </div>
                        </a>
                    </div>
                </div>
            {/each}
        </div>
    </div>
</div>