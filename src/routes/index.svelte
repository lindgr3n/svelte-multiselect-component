<script lang="ts">
	import '../tailwind.css';
	import Multiselect from '$lib/components/ds/molecules/Multiselect.svelte';
	import Badge from '$lib/components/ds/atoms/Badge.svelte';

	let tags: Tag[] = [];
	const data = [
		{
			name: 'zoega',
			label: 'Zoega',
			icon: '',
			color: ''
		},
		{
			name: 'lofbergs',
			label: 'Löfbergs',
			icon: '',
			color: ''
		},
		{
			name: 'lavazza',
			label: 'Lavazza',
			icon: '',
			color: ''
		},
		{
			name: 'gevalia',
			label: 'Gevalia',
			icon: '',
			color: ''
		}
	];

	function onSelect(evt: CustomEvent) {
		const { selected } = evt.detail;

		tags = selected;
	}
</script>

<div class="flex flex-col pb-8">
	<p>Selected:</p>
	<div>
		{#each tags as tag}
			<Badge canClose={false}>{tag.label}</Badge>
		{/each}
	</div>
</div>
<Multiselect {data} on:save={onSelect} />
