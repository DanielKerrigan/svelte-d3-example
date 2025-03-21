<script>
	import * as d3 from 'd3';
	import Axis from './Axis.svelte';

	let { dataset, width, height, marginLeft, marginTop, marginRight, marginBottom, feature, color } =
		$props();

	const counts = $derived(
		d3.rollup(
			dataset,
			(g) => g.length,
			(d) => d[feature]
		)
	);

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

<svg {width} {height}>
	<g>
		{#each counts as [category, count] (category)}
			<rect
				x={x(0)}
				width={x(count) - x(0)}
				y={y(category)}
				height={y.bandwidth()}
				fill={color(category)}
			/>
		{/each}
	</g>

	<Axis orientation="left" scale={y} {width} {height} {marginLeft} {marginBottom} label="" />

	<Axis orientation="bottom" scale={x} {width} {height} {marginLeft} {marginBottom} label="Count" />
</svg>

<style>
	rect {
		transition: width 250ms;
	}
</style>
