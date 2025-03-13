<script>
	import * as d3 from 'd3';
	import Axis from './Axis.svelte';

	let {
		dataset,
		width,
		height,
		marginLeft,
		marginTop,
		marginRight,
		marginBottom,
		feature,
		selectedIndices,
		color
	} = $props();

	// filter the dataset by index
	const filteredDataset = $derived(selectedIndices.map((i) => dataset[i]));

	// get the counts for the filtered dataset
	const counts = $derived(
		d3.rollup(
			filteredDataset,
			(g) => g.length,
			(d) => d[feature]
		)
	);

	// scales

	const maxCount = $derived(d3.max(counts.values()));

	const x = $derived(
		d3
			.scaleLinear()
			.domain([0, maxCount])
			.nice()
			.range([marginLeft, width - marginRight])
	);

	const y = $derived(
		d3
			.scaleBand()
			.domain(color.domain())
			.range([marginTop, height - marginBottom])
			.padding(0.1)
	);
</script>

<svg {height} {width}>
	<!-- bars -->
	<g>
		{#each counts as [category, count] (category)}
			<rect
				x={x(0)}
				y={y(category)}
				height={y.bandwidth()}
				width={x(count) - x(0)}
				fill={color(category)}
			/>
		{/each}
	</g>

	<!-- axes -->
	<Axis orientation="bottom" scale={x} {width} {height} {marginLeft} {marginBottom} label="Count" />
	<Axis orientation="left" scale={y} {width} {height} {marginLeft} {marginBottom} />
</svg>

<style>
	/* animate changes to the lengths of the bars */
	rect {
		transition: width 250ms;
	}
</style>
