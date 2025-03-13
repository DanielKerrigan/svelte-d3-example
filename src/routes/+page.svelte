<script>
	import './style.css';
	import PlayerList from '$lib/components/PlayerList.svelte';
	import ScatterPlot from '$lib/components/ScatterPlot.svelte';

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
</script>

<div class="container">
	<div class="header"></div>
	<div class="main">
		<div class="player-list">
			<PlayerList dataset={data.dataset} />
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
			/>
		</div>

		<div class="scatter-plot">
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

	.scatter-plot {
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
