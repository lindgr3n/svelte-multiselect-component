<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import { fade } from 'svelte/transition';

	import Badge from '../atoms/Badge.svelte';

	const dispatch = createEventDispatcher();

	export let data: Tag[] = [];
	export let selected: Tag[] = [];
	let filteredList: Tag[] = [];
	let isOpen = false;
	let search = '';
	let selectedIndex = -1;

	$: {
		filteredList = data.filter(
			(tag) => !selected.find((selectedTag) => selectedTag.name === tag.name)
		);
	}

	function onAddItem(item: Tag) {
		selected = [...selected, item];
		onSave();
	}

	function onRemoveItem(item: Tag) {
		selected = selected.filter((tag) => tag.name !== item.name);
		onSave();
	}

	function onSave() {
		dispatch('save', { selected });
	}

	function onInput() {
		if (!search) {
			filteredList = data.filter(
				(tag) =>
					!selected.find(
						(selectedTag) => selectedTag.label.toLowerCase() === tag.label.toLowerCase()
					)
			);
		}

		filteredList = filteredList.filter((tag) =>
			tag.label.toLowerCase().includes(search.toLowerCase())
		);
	}

	function onEnter(evt: KeyboardEvent) {
		if (evt.key !== 'Enter') {
			evt.preventDefault();
			return;
		}

		if (!search && selectedIndex === -1) {
			return;
		}

		if (filteredList.length === 1) {
			selectedIndex = 0;
			search = '';
		}

		if (selectedIndex != -1) {
			selected = [...selected, ...filteredList.splice(selectedIndex, 1)];
			filteredList = [
				...filteredList.slice(0, selectedIndex),
				...filteredList.slice(selectedIndex + 1)
			];
			selectedIndex = 0;
			return;
		}
		// If no item is selcted we create it
		const tag: Tag = {
			name: search,
			label: search,
			color: '',
			icon: ''
		};

		selected = [...selected, tag];
		data.push(tag);
		search = '';
		onSave();
	}

	function onNavigation(evt: KeyboardEvent) {
		switch (evt.key) {
			case 'ArrowUp':
				selectedIndex--;
				break;
			case 'ArrowDown':
				selectedIndex++;
				break;
			case 'Escape':
				break;

			default:
				return;
		}

		if (selectedIndex < 0) {
			selectedIndex = 0;
		}
		if (selectedIndex > filteredList.length - 1) {
			selectedIndex = filteredList.length - 1;
		}
	}
</script>

<div class="mt-1 relative">
	<button
		type="button"
		class="flex relative w-full bg-white border border-gray-300 rounded-md shadow-sm pl-3 pr-10 py-2 text-left cursor-default focus:outline-none focus:ring-1 focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
		aria-haspopup="listbox"
		aria-expanded="true"
		aria-labelledby="listbox-label"
		on:click={() => (isOpen = !isOpen)}
		on:blur={() => (isOpen = false)}
	>
		<div class="flex flex-wrap items-center gap-x-2 w-full">
			{#each selected as item}
				<Badge on:close={(evt) => onRemoveItem(item)}>{item.label}</Badge>
			{/each}
			<input
				type="text"
				class="h-6 border-none ring-transparent focus:ring-transparent focus:outline-none"
				bind:value={search}
				on:input={onInput}
				on:keyup={onEnter}
				on:keyup={onNavigation}
			/>
		</div>
		<span class="absolute inset-y-0 right-0 flex items-center pr-2 pointer-events-none">
			<!-- Heroicon name: solid/selector -->
			<svg
				class="h-5 w-5 text-gray-400"
				xmlns="http://www.w3.org/2000/svg"
				viewBox="0 0 20 20"
				fill="currentColor"
				aria-hidden="true"
			>
				<path
					fill-rule="evenodd"
					d="M10 3a1 1 0 01.707.293l3 3a1 1 0 01-1.414 1.414L10 5.414 7.707 7.707a1 1 0 01-1.414-1.414l3-3A1 1 0 0110 3zm-3.707 9.293a1 1 0 011.414 0L10 14.586l2.293-2.293a1 1 0 011.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 010-1.414z"
					clip-rule="evenodd"
				/>
			</svg>
		</span>
	</button>

	{#if isOpen}
		<ul
			class="absolute z-10 mt-1 w-full bg-white shadow-lg max-h-60 rounded-md py-1 text-base ring-1 ring-black ring-opacity-5 overflow-auto focus:outline-none sm:text-sm"
			tabindex="-1"
			role="listbox"
			aria-labelledby="listbox-label"
			aria-activedescendant="listbox-option-3"
			transition:fade={{ duration: 100 }}
		>
			{#each filteredList as item, index}
				<li
					class="text-gray-900 cursor-default select-none relative py-2 pl-3 pr-9 hover:bg-blue-200"
					id="listbox-option-0"
					role="option"
					on:click={(e) => onAddItem(item)}
					class:bg-blue-200={index === selectedIndex}
				>
					<Badge canClose={false}>{item.label}</Badge>
				</li>
			{/each}
		</ul>
	{/if}
</div>
