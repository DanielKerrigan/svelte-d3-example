<script>
	let { dataset } = $props();
</script>

<ul>
	{#each dataset as player (player.player_id)}
		<li>{player.name} ({player.team})</li>
	{/each}
</ul>

<style>
	ul {
		width: 18em;
		max-height: 100%;
		overflow: auto;
		list-style: none;
		padding: 0;
	}

	li + li {
		margin-top: 0.5em;
	}
</style>
