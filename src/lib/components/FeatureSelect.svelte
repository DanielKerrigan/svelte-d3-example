<script>
	let { label, options, value, onChangeValue } = $props();
</script>

<div class="container">
	<div>{label}</div>
	<select {value} onchange={(event) => onChangeValue(event.target.value)}>
		{#each options as option (option)}
			<option>{option}</option>
		{/each}
	</select>
</div>

<style>
	.container {
		display: flex;
		align-items: center;
		gap: 0.25em;
	}
</style>
