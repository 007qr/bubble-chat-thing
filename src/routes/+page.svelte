<script lang="ts">
	import BubbleLooper from './BubbleLooper.svelte';

	const lines = ['Checking status…', 'All good. Want me to proceed?', 'Done. Next step?'];
</script>

<div class="mx-auto max-w-[800px]">
	<BubbleLooper texts={lines} typingMs={900} charMs={34} showMs={900} />
</div>
