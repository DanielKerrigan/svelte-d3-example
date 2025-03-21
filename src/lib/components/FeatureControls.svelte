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

	const axisColumns = $derived(
		dataset.columns.filter((col) => typeof dataset[0][col] === 'number')
	);

	const colorColumns = $derived(
		dataset.columns.filter((col) => {
			const uniqueValues = new Set(dataset.map((d) => d[col]));
			return uniqueValues.size <= 10;
		})
	);
</script>

<div>
	<FeatureSelect
		label="X-axis"
		options={axisColumns}
		value={xFeature}
		onChangeValue={onChangeXFeature}
	/>
	<FeatureSelect
		label="Y-axis"
		options={axisColumns}
		value={yFeature}
		onChangeValue={onChangeYFeature}
	/>
	<FeatureSelect
		label="Color"
		options={colorColumns}
		value={colorFeature}
		onChangeValue={onChangeColorFeature}
	/>
</div>

<style>
	div {
		display: flex;
		gap: 1em;
	}
</style>
