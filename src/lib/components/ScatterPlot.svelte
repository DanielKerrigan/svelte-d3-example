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

	let svg;
	let brushedPlayerIDs = $state(null);

	const brush = $derived(
		d3
			.brush()
			.extent([
				[marginLeft, marginTop],
				[width - marginRight, height - marginBottom]
			])
			.on('start brush end', brushed)
	);

	function brushed(event) {
		if (event.selection) {
			const [[x0, y0], [x1, y1]] = event.selection;

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

	$effect(() => {
		xFeature;
		yFeature;
		d3.select(svg).call(brush).call(brush.clear);
	});
</script>

<svg {width} {height} bind:this={svg}>
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
