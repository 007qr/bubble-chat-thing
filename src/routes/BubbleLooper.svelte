<script lang="ts">
	type Phase = 'typing' | 'message';

	let {
		texts = ['First message', 'Second message', 'Third message'],
		typingMs = 1100,
		charMs = 28,
		showMs = 900,
		side = 'left' as 'left' | 'right'
	} = $props<{
		texts?: string[];
		typingMs?: number;
		charMs?: number;
		showMs?: number;
		side?: 'left' | 'right';
	}>();

	let phase = $state<Phase>('typing');
	let index = $state(0);
	let typed = $state('');

	const filtered = $derived.by<string[]>(() =>
		(texts ?? []).map((t) => (t ?? '').trim()).filter(Boolean)
	);

	const runKey = $derived.by(() => `${filtered.join('\u0000')}|${typingMs}|${charMs}|${showMs}`);

	function sleep(ms: number, signal: AbortSignal) {
		return new Promise<void>((resolve) => {
			const id = window.setTimeout(resolve, ms);
			signal.addEventListener(
				'abort',
				() => {
					clearTimeout(id);
					resolve();
				},
				{ once: true }
			);
		});
	}

	$effect(() => {
		if (typeof window === 'undefined') return;

		runKey;

		const ctrl = new AbortController();

		phase = 'typing';
		index = 0;
		typed = '';

		(async () => {
			while (!ctrl.signal.aborted) {
				if (filtered.length === 0) {
					phase = 'typing';
					typed = '';
					await sleep(300, ctrl.signal);
					continue;
				}

				phase = 'typing';
				typed = '';
				await sleep(Math.max(0, typingMs), ctrl.signal);
				if (ctrl.signal.aborted) break;

				const text = filtered[index % filtered.length];
				const chars = Array.from(text);
				const perChar = Math.max(0, charMs);

				phase = 'message';
				typed = '';

				for (let i = 0; i < chars.length; i++) {
					if (ctrl.signal.aborted) break;
					typed += chars[i];
					await sleep(perChar, ctrl.signal);
				}
				if (ctrl.signal.aborted) break;

				await sleep(Math.max(0, showMs), ctrl.signal);
				if (ctrl.signal.aborted) break;

				phase = 'typing';
				index = (index + 1) % filtered.length;
			}
		})();

		return () => ctrl.abort();
	});
</script>

<div class="row {side}">
	<div class="bubble {phase}" aria-live="polite">
		{#if phase === 'typing'}
			<span class="dots" aria-label="Typing">
				<span class="dot d1"></span>
				<span class="dot d2"></span>
				<span class="dot d3"></span>
			</span>
		{:else}
			<span class="msg" aria-label="Message">{typed}</span>
		{/if}
	</div>
</div>

<style>
	.row {
		display: flex;
		margin: 14px 0;
	}
	.row.left {
		justify-content: flex-start;
	}
	.row.right {
		justify-content: flex-end;
	}

	.bubble {
		background: #0a84ff;
		color: rgba(255, 255, 255, 0.98);
		max-width: min(540px, 78vw);
		padding: 10px 14px;
		border-radius: 18px;
		box-shadow: 0 10px 28px rgba(0, 0, 0, 0.12);
		transition:
			max-width 260ms cubic-bezier(0.2, 0.8, 0.2, 1),
			border-radius 260ms cubic-bezier(0.2, 0.8, 0.2, 1),
			padding 260ms cubic-bezier(0.2, 0.8, 0.2, 1);
	}

	.row.left .bubble {
		border-bottom-left-radius: 0;
	}

	.row.right .bubble {
		border-bottom-right-radius: 0;
	}

	.bubble.typing {
		max-width: 76px;
		border-radius: 999px;
		padding: 10px 12px;
	}

	.row.left .bubble.typing {
		border-bottom-left-radius: 0;
	}

	.row.right .bubble.typing {
		border-bottom-right-radius: 0;
	}

	.msg {
		font-size: 15px;
		line-height: 1.35;
		letter-spacing: 0.1px;
		white-space: pre-wrap;
		word-break: break-word;
	}

	.dots {
		display: inline-flex;
		gap: 6px;
		align-items: center;
		height: 16px;
	}

	.dot {
		width: 7px;
		height: 7px;
		border-radius: 999px;
		background: rgba(255, 255, 255, 0.95);
		opacity: 0.55;
		animation: bounce 900ms infinite ease-in-out;
	}

	.d1 {
		animation-delay: 0ms;
	}
	.d2 {
		animation-delay: 120ms;
	}
	.d3 {
		animation-delay: 240ms;
	}

	@keyframes bounce {
		0%,
		100% {
			transform: translateY(0);
			opacity: 0.55;
		}
		50% {
			transform: translateY(-3px);
			opacity: 0.95;
		}
	}
</style>
