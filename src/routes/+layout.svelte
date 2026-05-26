<script lang="ts">
	import { page } from '$app/state';
	import { fade } from 'svelte/transition';
    import { cubicOut } from 'svelte/easing';

	import './layout.css';
	let { children } = $props();

	const links = [
		{ href: '/', label: 'Home' },
		{ href: '/explore', label: 'Explore' },
		{ href: '/about', label: 'About' }
	];

	function isActive(href: string) {
		const path = page.url.pathname;
		return href === '/' ? path === '/' : path === href || path.startsWith(href + '/');
	}
</script>

<nav
	class="fixed bottom-0 left-0 z-100 flex w-full justify-between
		bg-paper
		bg-postcard
		bg-size-[18px_18px,100%_100%]
		shadow-[0_-10px_30px_-15px_rgba(0,0,0,0.25)]
		font-sans"
>
	<!-- TODO: make this reuseable if keeping -->
	<div
		aria-hidden="true"
		class="pointer-events-none absolute top-0 left-0 right-0 h-[2px] opacity-50
			bg-[linear-gradient(to_right,rgba(0,0,0,0.35)_0_6px,transparent_6px_12px)]
			bg-size-[12px_2px]"
	></div>

	{#each links as { href, label }}
		{@const active = isActive(href)}
		<a
			{href}
			class="group relative flex w-1/3 items-center justify-center gap-2 py-4
				text-xs font-semibold uppercase tracking-[0.22em]
				transition-colors
				{active ? 'text-[#1c1814]' : 'text-[#7a6a52] hover:text-[#1c1814]'}"
		>
			<span
				class="inline-block h-1.5 w-1.5 rounded-full bg-orange-700
					transition-opacity
					{active ? 'opacity-100' : 'opacity-0 group-hover:opacity-100'}"
				aria-hidden="true"
			></span>
			<span>{label}</span>
		</a>
	{/each}
</nav>

<!-- {#key page.url.pathname}
    <div
        in:fade={{ duration: 200, delay: 150, easing: cubicOut }}
        out:fade={{ duration: 150, easing: cubicOut }}
    >
        {@render children()}
    </div>
{/key} -->

{@render children()}