<script>
	import './style.css';
	import * as d3 from 'd3';
	import ScatterPlot from '$lib/components/ScatterPlot.svelte';
	import FeatureControls from '$lib/components/FeatureControls.svelte';
	import BarChart from '$lib/components/BarChart.svelte';
	import PlayerList from '$lib/components/PlayerList.svelte';
	import ColorLegend from '$lib/components/ColorLegend.svelte';
	import RainCloudPlot from '$lib/components/RainCloudPlot.svelte';

	// data comes from the load function in +page.js
	let { data } = $props();

	// default features to visualize
	let xFeature = $state('strikeout');
	let yFeature = $state('hit');
	let colorFeature = $state('all_star');

	// dimensions
	let width = $state(400);
	let height = $state(400);
	const size = $derived(Math.min(width, height));

	// brushed data points in the scatter plot
	let filteredDataset = $state([]);

	// callback function to update filteredDataset when the scatterplot is brushed
	function onbrush(brushedDataPoints) {
		filteredDataset = brushedDataPoints;
	}

	// data point that is highlighted in the list
	let highlightedPlayer = $state(null);

	// callback function to update highlightedPlayer when the list is hovered over
	function onhover(player) {
		highlightedPlayer = player;
	}

	// get the unique categories in the dataset sorted by count
	const categories = $derived(
		d3
			.groupSort(
				data.dataset,
				(g) => g.length,
				(d) => d[colorFeature]
			)
			.reverse()
	);

	const color = $derived(d3.scaleOrdinal().domain(categories).range(d3.schemeTableau10));
</script>

<div class="container">
	<div class="header">
		<FeatureControls
			dataset={data.dataset}
			{xFeature}
			{yFeature}
			{colorFeature}
			onChangeXFeature={(value) => (xFeature = value)}
			onChangeYFeature={(value) => (yFeature = value)}
			onChangeColorFeature={(value) => (colorFeature = value)}
		/>
		<ColorLegend {color} {colorFeature} />
	</div>
	<div class="main">
		<div class="player-list">
			<PlayerList dataset={filteredDataset} {onhover} />
		</div>
		<div class="scatter-plot" bind:clientWidth={width} bind:clientHeight={height}>
			<ScatterPlot
				dataset={data.dataset}
				width={size}
				height={size}
				marginLeft={64}
				marginTop={32}
				marginRight={32}
				marginBottom={64}
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
				marginTop={32}
				marginRight={32}
				marginBottom={64}
				feature={colorFeature}
				{color}
			/>
		</div>

		<div class="rain-cloud-plot">
			<RainCloudPlot
				dataset={data.dataset}
				width={size}
				height={size}
				marginLeft={64}
				marginTop={32}
				marginRight={32}
				marginBottom={64}
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
		/* make the div take up the entire screen */
		height: 100vh;
		width: 100vw;
		/* add 32px of padding around the div */
		padding: 2em;
		/* put the controls on top of the plots with 32px of space in between */
		display: flex;
		flex-direction: column;
		gap: 2em;
	}

	/* place the feature controls and color legend next to each other */
	.header {
		display: flex;
		gap: 2em;
		align-items: center;
	}

	.main {
		/* make this div take up the rest of the container */
		flex: 1;
		/* allow the div to shrink */
		min-height: 0;
		/* place the children next to each other */
		display: flex;
		/* add space between them */
		gap: 2em;
	}

	.scatter-plot,
	.bar-chart,
	.rain-cloud-plot {
		/* take up half of the available horizontal space in main*/
		flex: 1;
		/* be as tall as main */
		height: 100%;
		/* center chart in div */
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
