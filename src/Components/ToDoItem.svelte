<script>
	export let checked
	export let name
	export let id
	export let handleCheck
	export let deleteItem
</script>


<input id={id} type="checkbox" bind:checked={checked} on:change={handleCheck(id)}>
<label for={id}>{name}</label>
<button class="delete-btn" on:click={deleteItem(id)}>×</button>


<style>
	input {
		margin: 0;
	}

	input[type=checkbox]:checked + label,
	input[type=radio]:checked + label {
		color: #999;
	}

	.delete-btn {
		padding: .25rem;
		margin: 0;
		border: none;
		background: transparent;
		transform: scale(2);
		position: absolute;
		right: .5em;
		top: .85em;
		line-height: 0;
	}
</style>
