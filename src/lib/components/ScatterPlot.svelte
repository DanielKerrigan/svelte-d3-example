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
		xFeature,
		yFeature,
		colorFeature
	} = $props();

	const x = $derived(
		d3
			.scaleLinear()
			.domain(d3.extent(dataset, (d) => d[xFeature]))
			.nice()
			.range([marginLeft, width - marginRight])
	);

	const y = $derived(
		d3
			.scaleLinear()
			.domain(d3.extent(dataset, (d) => d[yFeature]))
			.nice()
			.range([height - marginBottom, marginTop])
	);

	const color = $derived(
		d3
			.scaleOrdinal()
			.domain(new Set(dataset.map((d) => d[colorFeature])))
			.range(d3.schemeCategory10)
	);
</script>

<svg {width} {height}>
	<g>
		{#each dataset as d (d.player_id)}
			<circle cx={x(d[xFeature])} cy={y(d[yFeature])} fill={color(d[colorFeature])} r={3} />
		{/each}
	</g>

	<Axis
		orientation="left"
		scale={y}
		{width}
		{height}
		{marginLeft}
		{marginBottom}
		label={yFeature}
	/>

	<Axis
		orientation="bottom"
		scale={x}
		{width}
		{height}
		{marginLeft}
		{marginBottom}
		label={xFeature}
	/>
</svg>

<style>
</style>
