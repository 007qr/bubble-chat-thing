<script lang="ts">
	import Logo from './_components/Logo.svelte';
	import CustomLinesEditor from './_components/CustomLinesEditor.svelte';
	import BubbleLooper from './BubbleLooper.svelte';

	let lines = $state(['Checking status...', 'All good. Want me to proceed?', 'Done. Next step?']);
</script>

<main class="relative h-screen w-full overflow-hidden bg-black text-white">
	<section class="mx-auto mt-12 flex h-[calc(100dvh-5rem)] max-w-[320px] flex-col">
		<div class="h-10 w-10">
			<Logo />
		</div>

		<div class="mt-30 flex flex-1 flex-col items-center gap-10">
			<span class="font-instrument text-[33px] leading-tight tracking-[-0.02em] text-[#c5c3b9]"
				>is <i>this</i> you?</span
			>
			<div class="max-w-200 self-start">
				<BubbleLooper texts={lines} typingMs={900} showMs={900} />
			</div>
			<div class="absolute bottom-10 right-10 w-[320px] max-w-[90vw]">
				<CustomLinesEditor bind:lines />
			</div>
		</div>

		<div class="flex flex-col items-center justify-center gap-6 text-[#888888]">
			<p class="text-center text-[14px] text-white">
				Isla is a private AI companion for founders — a judgment-free place to vent, think, and
				reset.
			</p>
			<button
				class="flex h-14 w-full items-center justify-center rounded-[72px] bg-white/90 px-5 py-3 text-black backdrop-blur-[15px] transition-opacity hover:opacity-90 active:focus:opacity-80"
			>
				Chat with Isla
			</button>
		</div>
	</section>
</main>
