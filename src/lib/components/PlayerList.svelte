<script>
	import * as d3 from 'd3';

	let { dataset, selectedIndices, onhover } = $props();

	const players = $derived(
		selectedIndices.map((i) => dataset[i]).sort((a, b) => d3.ascending(a.name, b.name))
	);
</script>

<ul>
	{#each players as player (player.name)}
		<li
			onmouseover={() => onhover(player)}
			onmouseleave={() => onhover(null)}
			onfocus={() => onhover(player)}
			onfocusout={() => onhover(null)}
		>
			{player.name} ({player.team})
		</li>
	{/each}
</ul>

<style>
	ul {
		width: 18em;
		/* make div no taller than its parent */
		max-height: 100%;
		/* add scrolling */
		overflow: auto;
		/* remove bullet point */
		list-style: none;
		padding: 0;
	}

	/* add space between list items */
	li + li {
		margin-top: 0.5em;
	}

	/* set background color when hovering over list item */
	li:hover {
		background-color: #dddddd;
	}
</style>
