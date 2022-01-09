<script lang="ts">
	import { createEventDispatcher } from 'svelte';

	let className = '';

	export { className as class };
	export let canClose = true;

	const dispatch = createEventDispatcher();

	function onClose(evt: MouseEvent) {
		evt.preventDefault();
		evt.stopPropagation();
		dispatch('close');
	}
</script>

<span
	class="inline-flex rounded-full items-center py-0.5 pl-2.5 pr-2 text-sm font-medium bg-blue-100 text-blue-700 {className}"
	class:pr-2.5={!canClose}
>
	<slot />
	{#if canClose}
		<button
			type="button"
			class="flex-shrink-0 h-4 w-4 ml-0.5 rounded-full inline-flex items-center justify-center text-blue-400 hover:bg-blue-200 hover:text-blue-500 focus:outline-none focus:bg-blue-500 focus:text-white"
			on:click={onClose}
		>
			<span class="sr-only">Remove option</span>
			<svg class="h-2 w-2" stroke="currentColor" fill="none" viewBox="0 0 8 8">
				<path stroke-linecap="round" stroke-width="1.5" d="M1 1l6 6m0-6L1 7" />
			</svg>
		</button>
	{/if}
</span>
