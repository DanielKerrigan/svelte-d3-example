<script>
	let { color } = $props();
</script>

<div class="legend">
	{#each color.domain() as d (d)}
		<div class="swatch">
			<!-- colored square -->
			<div class="square" style:background-color={color(d)}></div>
			<!-- label -->
			<div class="label">{d}</div>
		</div>
	{/each}
</div>

<style>
	.legend {
		display: flex;
		align-items: center;
		gap: 1em;
	}

	.swatch {
		display: flex;
		align-items: center;
		gap: 0.25em;
	}

	.square {
		width: 15px;
		height: 15px;
	}
</style>
