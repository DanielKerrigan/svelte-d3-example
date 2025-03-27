<script>
	import './style.css';
	import PlayerList from '$lib/components/PlayerList.svelte';
	import ScatterPlot from '$lib/components/ScatterPlot.svelte';
	import RainCloudPlot from '$lib/components/RainCloudPlot.svelte';
	import BarChart from '$lib/components/BarChart.svelte';
	import * as d3 from 'd3';
	import ColorLegend from '$lib/components/ColorLegend.svelte';
	import FeatureControls from '$lib/components/FeatureControls.svelte';

	// data comes from the load function in +page.js
	let { data } = $props();

	// default features to visualize
	let xFeature = $state('strikeout');
	let yFeature = $state('hit');
	let colorFeature = $state('all_star');

	let highlightedPlayer = $state(null);

	function onChangeHighlightedPlayer(value) {
		highlightedPlayer = value;
	}

	function onChangeXFeature(value) {
		xFeature = value;
	}

	// dimensions
	let width = $state(400);
	let height = $state(400);
	const size = $derived(Math.min(width, height));

	let filteredDataset = $state([]);

	// callback function that will update filteredDataset on brushing
	function onbrush(brushedDataPoints) {
		filteredDataset = brushedDataPoints;
	}

	const categories = $derived(
		d3
			.groupSort(
				data.dataset,
				(g) => g.length,
				(d) => d[colorFeature]
			)
			.reverse()
	);

	const color = $derived(d3.scaleOrdinal().domain(categories).range(d3.schemeCategory10));
</script>

<div class="container">
	<div class="header">
		<FeatureControls
			dataset={data.dataset}
			{xFeature}
			{yFeature}
			{colorFeature}
			{onChangeXFeature}
			onChangeYFeature={(value) => (yFeature = value)}
			onChangeColorFeature={(value) => (colorFeature = value)}
		/>
		<ColorLegend {color} />
	</div>
	<div class="main">
		<div class="player-list">
			<PlayerList dataset={filteredDataset} onHoverPlayer={onChangeHighlightedPlayer} />
		</div>

		<div class="scatter-plot" bind:clientWidth={width} bind:clientHeight={height}>
			<ScatterPlot
				dataset={data.dataset}
				width={size}
				height={size}
				marginLeft={64}
				marginBottom={64}
				marginTop={32}
				marginRight={32}
				{xFeature}
				{yFeature}
				{colorFeature}
				{color}
				{highlightedPlayer}
				{onbrush}
			/>
		</div>

		<div class="bar-chart">
			<BarChart
				dataset={filteredDataset}
				width={size}
				height={size}
				marginLeft={64}
				marginBottom={64}
				marginTop={32}
				marginRight={32}
				feature={colorFeature}
				{color}
			/>
		</div>

		<div class="raincloud-plot">
			<RainCloudPlot
				dataset={data.dataset}
				width={size}
				height={size}
				marginLeft={64}
				marginBottom={64}
				marginTop={32}
				marginRight={32}
				{xFeature}
				{colorFeature}
				{color}
			/>
		</div>
	</div>
</div>

<style>
	.container {
		/* set the font */
		font-family: system-ui, sans-serif;
		font-size: 16px;
		/* dimensions */
		height: 100vh;
		width: 100vw;
		/* padding */
		padding: 2em;
		/* layout */
		display: flex;
		flex-direction: column;
		gap: 2em;
	}

	.main {
		flex: 1;
		min-height: 0;
		display: flex;
		gap: 2em;
	}

	.header {
		display: flex;
		gap: 2em;
		align-items: center;
	}

	.bar-chart,
	.scatter-plot,
	.raincloud-plot {
		/* take up the extra horizontal space of main */
		flex: 1;
		/* be as tall as main */
		height: 100%;
		/* center visualization in div */
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
