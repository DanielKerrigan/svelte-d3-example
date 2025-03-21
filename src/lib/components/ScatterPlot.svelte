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
		colorFeature,
		color,
		highlightedPlayer
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
</script>

<svg {width} {height}>
	<g>
		{#each dataset as d (d.player_id)}
			<circle cx={x(d[xFeature])} cy={y(d[yFeature])} fill={color(d[colorFeature])} r={3} />
		{/each}

		{#if highlightedPlayer}
			<circle
				cx={x(highlightedPlayer[xFeature])}
				cy={y(highlightedPlayer[yFeature])}
				fill={color(highlightedPlayer[colorFeature])}
				r={6}
				stroke="black"
				stroke-width={2}
			/>
		{/if}
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
	circle {
		transition:
			cx 250ms,
			cy 250ms;
	}
</style>
