<script>
	let { orientation, scale, marginLeft, marginBottom, width, height, label } = $props();

	const ticks = $derived(scale.bandwidth ? scale.domain() : scale.ticks());
	const offset = $derived(scale.bandwidth ? scale.bandwidth() / 2 : 0);
</script>

<g>
	{#if orientation === 'left'}
		<g transform="translate({marginLeft})">
			{#each ticks as tick (tick)}
				<g transform="translate(0,{scale(tick) + offset})">
					<line x2={-6} stroke="black" />
					<text text-anchor="end" dominant-baseline="middle" fill="black" x={-10}>{tick}</text>
				</g>
			{/each}
		</g>

		{#if label}
			<text text-anchor="start" dominant-baseline="hanging" x={0} y={0} fill="black">
				{label}
			</text>
		{/if}
	{:else}
		<g transform="translate(0,{height - marginBottom})">
			{#each ticks as tick (tick)}
				<g transform="translate({scale(tick) + offset},0)">
					<line y2={6} stroke="black" />
					<text text-anchor="middle" dominant-baseline="hanging" fill="black" y={10}>{tick}</text>
				</g>
			{/each}

			{#if label}
				<text text-anchor="end" dominant-baseline="hanging" x={width} y={32} fill="black">
					{label}
				</text>
			{/if}
		</g>
	{/if}
</g>

<style>
</style>
