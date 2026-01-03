<script lang="ts">
	let { lines = $bindable<string[]>([]) } = $props<{
		lines?: string[];
	}>();

	let newLine = $state('');

	const addLine = () => {
		const next = newLine.trim();
		if (!next) return;
		lines = [...lines, next];
		newLine = '';
	};

	const handleSubmit = (event: SubmitEvent) => {
		event.preventDefault();
		addLine();
	};

	const removeLine = (index: number) => {
		lines = lines.filter((_, i) => i !== index);
	};

	const clearLines = () => {
		lines = [];
	};
</script>

<div class="w-full rounded-[28px] border border-white/10 bg-white/5 p-4 text-left">
	<p class="text-[12px] uppercase tracking-[0.28em] text-white/50">Custom lines</p>
	<form class="mt-3 flex gap-2" onsubmit={handleSubmit}>
		<input
			class="h-10 flex-1 rounded-full border border-white/10 bg-black/40 px-4 text-[13px] text-white placeholder:text-white/40 focus:border-white/30 focus:outline-none"
			placeholder="Add a line and press Enter"
			bind:value={newLine}
		/>
		<button
			type="submit"
			class="h-10 rounded-full bg-white/90 px-4 text-[12px] font-semibold uppercase tracking-[0.18em] text-black transition hover:opacity-90"
		>
			Add
		</button>
	</form>

	{#if lines.length === 0}
		<p class="mt-3 text-[12px] text-white/40">No custom lines yet.</p>
	{:else}
		<ul class="mt-3 flex flex-col gap-2 text-[13px] text-white/90">
			{#each lines as line, index (line + index)}
				<li class="flex items-center justify-between gap-3 rounded-2xl bg-black/40 px-3 py-2">
					<span class="flex-1 break-words">{line}</span>
					<button
						type="button"
						class="text-[11px] font-semibold uppercase tracking-[0.18em] text-white/60 transition hover:text-white"
						onclick={() => removeLine(index)}
					>
						Remove
					</button>
				</li>
			{/each}
		</ul>
	{/if}

	<div class="mt-3 flex items-center justify-between text-[11px] text-white/50">
		<span>{lines.length} lines</span>
		<button
			type="button"
			class="font-semibold uppercase tracking-[0.18em] text-white/60 transition hover:text-white"
			onclick={clearLines}
		>
			Clear
		</button>
	</div>
</div>
