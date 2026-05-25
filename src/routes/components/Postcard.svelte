<script lang="ts">
	let { title = 'History on Stamps', tagline = 'Philately in Troubled Times' } = $props();
</script>

<article
	class="fixed inset-0 z-20 grid place-items-center p-4 pb-20 sm:pb-4 pointer-events-none"
	aria-label="Welcome card"
>
	<div
		class="pointer-events-auto relative max-w-[720px] overflow-hidden rounded-md text-[#2b2620] grid grid-cols-1 sm:grid-cols-[1.4fr_1fr] rotate-[-0.4deg]
			bg-paper bg-postcard bg-size-postcard shadow-postcard
			font-Inter"
	>
		<!-- title + body text -->
		<div class="flex flex-col justify-center min-w-0 px-6 pt-7 pb-4 sm:p-8">
			<div class="inline-flex items-center gap-2 text-xs uppercase tracking-[0.18em] font-medium text-[#7a6a52]">
				<span class="inline-block h-1.5 w-1.5 rounded-full bg-orange-700"></span>
				<span>Est. 2026 · Worldwide</span>
			</div>

			<h1
				class="font-fraunces font-black leading-[0.95] tracking-[-0.02em] text-[#1c1814]
					mt-3 mb-1 text-[clamp(2.5rem,6vw,4.25rem)]"
			>
				{title}
			</h1>
			<p
				class="font-fraunces italic font-medium text-[#5a4a32] m-0
					text-[clamp(1rem,1.6vw,1.15rem)]"
			>
				{tagline}
			</p>

			<div
				aria-hidden="true"
				class="h-px my-4 bg-[linear-gradient(to_right,transparent,rgba(0,0,0,0.2),transparent)]"
			></div>

			<p class="text-base leading-[1.55] text-[#3d342a] m-0 mb-2 max-w-[38ch]">
				"Postage stamps are the most universal form of public imagery other than money."
			</p>
			<div class="italic mb-5 font-fraunces">Eric Hobsbawm</div>
		</div>

		<!-- div -->
		<div aria-hidden="true" class="postcard-divider"></div>

		<div
			aria-hidden="true"
			class="relative flex p-4 sm:p-6 min-h-50 sm:min-h-[220px]
				flex-row items-center justify-between
				sm:flex-col sm:items-end sm:justify-between"
		>
			<!-- TODO: use stamp image instead of failed CSS? -->
			<!-- <div
				class="w-[72px] h-[86px] sm:w-[92px] sm:h-[110px] p-1.5 bg-[#fdf9f1]
					filter-[drop-shadow(0_2px_3px_rgba(0,0,0,0.15))]
					[-webkit-mask:radial-gradient(circle_4px_at_4px_50%,transparent_98%,#000)_0_0/12px_12px_repeat-y,radial-gradient(circle_4px_at_50%_4px,transparent_98%,#000)_0_0/12px_12px_repeat-x]
					[mask:radial-gradient(circle_4px_at_4px_50%,transparent_98%,#000)_0_0/12px_12px_repeat-y,radial-gradient(circle_4px_at_50%_4px,transparent_98%,#000)_0_0/12px_12px_repeat-x]
					[-webkit-mask-composite:source-in,source-in] mask-intersect"
			>
				<div
					class="w-full h-full flex flex-col items-center justify-center gap-1
						border border-dashed border-black/35 text-[#5a2a0a]
						[font-family:var(--font-display)]
						bg-[repeating-linear-gradient(45deg,rgba(194,65,12,0.12)_0_6px,transparent_6px_12px),linear-gradient(135deg,#f8d7a8,#e8a866)]"
				>
					<span class="text-3xl font-black leading-none">∞</span>
					<span class="text-[0.7rem] tracking-[0.2em] font-bold">PHITT</span>
				</div>
			</div> -->

			<!-- "ink" stamp -->
			<div class="flex items-center gap-2 rotate-[-8deg] opacity-55 m-0 sm:mr-auto sm:mt-auto">
				<div class="relative flex flex-col items-center justify-center text-center text-[#1c1814]
					w-16 h-16 sm:w-[78px] sm:h-[78px] rounded-full border-[1.5px] border-[#1c1814]
					px-1.5 text-[0.55rem] tracking-[0.15em] font-bold">
					<div class="absolute inset-1.5 rounded-full border border-[#1c1814]"></div>
					<span class="relative">PAR AVION</span>
					<span class="relative mt-1 text-[0.6rem] tracking-widest">15 · V · 26</span>
				</div>
				<div class="flex flex-col gap-1">
					<span class="block w-11 sm:w-[60px] h-[1.5px] rounded-sm bg-[#1c1814]"></span>
					<span class="block w-11 sm:w-[60px] h-[1.5px] rounded-sm bg-[#1c1814]"></span>
					<span class="block w-11 sm:w-[60px] h-[1.5px] rounded-sm bg-[#1c1814]"></span>
					<span class="block w-11 sm:w-[60px] h-[1.5px] rounded-sm bg-[#1c1814]"></span>
				</div>
			</div>
		</div>
	</div>
</article>
