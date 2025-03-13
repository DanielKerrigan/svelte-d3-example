<script>
	import FeatureSelect from './FeatureSelect.svelte';

	let {
		dataset,
		xFeature,
		yFeature,
		colorFeature,
		onChangeXFeature,
		onChangeYFeature,
		onChangeColorFeature
	} = $props();

	// we only want the x and y axes to be quantitative columns
	const axisColumns = $derived(
		dataset.columns.filter((col) => typeof dataset[0][col] === 'number')
	);

	// we only want the color of the circles to be categorical columns with 10 categories or fewer
	const colorColumns = $derived(
		dataset.columns.filter((col) => {
			const numUnique = new Set(dataset.map((d) => d[col])).size;
			return numUnique <= 10;
		})
	);
</script>

<div>
	<FeatureSelect
		value={xFeature}
		options={axisColumns}
		label="X-axis"
		onchange={onChangeXFeature}
	/>

	<FeatureSelect
		value={yFeature}
		options={axisColumns}
		label="Y-axis"
		onchange={onChangeYFeature}
	/>

	<FeatureSelect
		value={colorFeature}
		options={colorColumns}
		label="Color"
		onchange={onChangeColorFeature}
	/>
</div>

<style>
	/* place the dropdown menus next to each other with 16px of space in between */
	div {
		display: flex;
		gap: 1em;
	}
</style>
