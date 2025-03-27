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
		highlightedPlayer,
		onbrush
	} = $props();

	// scales

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

	// brushing
	// https://observablehq.com/@d3/brushable-scatterplot

	let svg;

	let brushedPlayerIDs = $state(null);

	function brushed(event) {
		if (event.selection) {
			const [[x0, y0], [x1, y1]] = event.selection;
			// filter to get the points in the brush
			const brushedDataPoints = dataset.filter((d) => {
				const dx = x(d[xFeature]);
				const dy = y(d[yFeature]);
				return dx >= x0 && dx <= x1 && dy >= y0 && dy <= y1;
			});
			brushedPlayerIDs = brushedDataPoints.map((d) => d.player_id);
			onbrush(brushedDataPoints);
		} else {
			brushedPlayerIDs = null;
			onbrush(dataset);
		}
	}

	const brush = $derived(
		d3
			.brush()
			.extent([
				[marginLeft, marginTop],
				[width - marginRight, height - marginBottom]
			])
			.on('start brush end', brushed)
	);

	// reset the brush when the visualization changes
	$effect(() => {
		xFeature;
		yFeature;
		d3.select(svg).call(brush).call(brush.clear);
	});
</script>

<svg {height} {width} bind:this={svg}>
	<!-- circles -->
	<g>
		{#each dataset as d (d.player_id)}
			<circle
				cx={x(d[xFeature])}
				cy={y(d[yFeature])}
				fill={brushedPlayerIDs === null || brushedPlayerIDs.includes(d.player_id)
					? color(d[colorFeature])
					: '#d3d3d3'}
				r={3}
			/>
		{/each}

		<!-- redraw circle for the highlighted player so that it appears on top -->
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

	<!-- axes -->
	<Axis
		orientation="bottom"
		scale={x}
		{width}
		{height}
		{marginLeft}
		{marginBottom}
		label={xFeature}
	/>
	<Axis
		orientation="left"
		scale={y}
		{width}
		{height}
		{marginLeft}
		{marginBottom}
		label={yFeature}
	/>
</svg>

<style>
	/* animate circles to their new location */
	circle {
		transition:
			cx 250ms,
			cy 250ms;
	}
</style>
