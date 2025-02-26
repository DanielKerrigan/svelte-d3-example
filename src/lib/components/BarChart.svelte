<script>
	import * as d3 from 'd3';
	import Axis from './Axis.svelte';

	let { dataset, feature, selectedIndices, color } = $props();

	// dimensions

	let width = $state(400);
	let height = $state(400);

	const margin = { top: 25, right: 20, bottom: 50, left: 60 };

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
			.range([margin.left, width - margin.right])
	);

	const y = $derived(
		d3
			.scaleBand()
			.domain(color.domain())
			.range([margin.top, height - margin.bottom])
			.padding(0.1)
	);
</script>

<div class="barchart" bind:clientWidth={width} bind:clientHeight={height}>
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
		<Axis orientation="bottom" scale={x} {width} {height} {margin} label={'Count'} />
		<Axis orientation="left" scale={y} {width} {height} {margin} />
	</svg>
</div>

<style>
	.barchart {
		/* take up extra horizontal space in the parent */
		flex: 1;
		/* be as tall as the parent div */
		height: 100%;
	}

	/* animate changes to the lengths of the bars */
	rect {
		transition: width 250ms;
	}
</style>
