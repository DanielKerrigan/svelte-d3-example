<script>
	import * as d3 from 'd3';
	import Axis from './Axis.svelte';
	import { density1d } from 'fast-kde';

	let {
		dataset,
		width,
		height,
		marginLeft,
		marginTop,
		marginRight,
		marginBottom,
		xFeature,
		colorFeature,
		color
	} = $props();

	const x = $derived(
		d3
			.scaleLinear()
			.domain(d3.extent(dataset, (d) => d[xFeature]))
			.nice()
			.range([marginLeft, width - marginRight])
	);

	const fy = $derived(
		d3
			.scaleBand()
			.domain(color.domain())
			.range([marginTop, height - marginBottom])
			.padding(0.1)
	);

	const groupHeight = $derived(fy.bandwidth());
	const tickHeight = $derived(groupHeight * 0.3);
	const gap = 2;
	const areaHeight = $derived(groupHeight - tickHeight - gap);
	const radius = 4;

	function getDataForGroup(data) {
		const xValues = data.map((d) => d[xFeature]);
		return {
			values: xValues,
			mean: d3.mean(xValues),
			density: Array.from(density1d(xValues, { extent: x.domain() }))
		};
	}

	const data = $derived(d3.rollup(dataset, getDataForGroup, (d) => d[colorFeature]));

	const maxY = $derived(
		d3.max(data, ([, raincloudData]) => d3.max(raincloudData.density, (d) => d.y))
	);

	const y = $derived(d3.scaleLinear().domain([0, maxY]).range([areaHeight, 0]));

	const area = $derived(
		d3
			.area()
			.x((d) => x(d.x))
			.y0((d) => y(d.y))
			.y1(y(0))
	);
</script>

<svg {width} {height}>
	<g>
		{#each data as [category, { values, mean, density }] (category)}
			<g transform="translate(0,{fy(category)})">
				<path d={area(density)} fill={color(category)} />

				<g>
					{#each values as value, i (i)}
						<line
							x1={x(value)}
							x2={x(value)}
							y1={areaHeight + gap}
							y2={areaHeight + gap + tickHeight}
							stroke={color(category)}
							stroke-opacity={0.5}
						/>
					{/each}

					<circle
						cx={x(mean)}
						cy={areaHeight + gap / 2}
						r={radius}
						fill="black"
						stroke="white"
						stroke-width={1.5}
					/>
				</g>
			</g>
		{/each}
	</g>

	<Axis
		orientation="left"
		scale={fy}
		{width}
		{height}
		{marginLeft}
		{marginBottom}
		label={colorFeature}
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
