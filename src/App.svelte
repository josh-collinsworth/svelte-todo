<script>
	import ToDoList from './Components/ToDoList.svelte'

	const listName = 'svelteToDo'

	const retrieveStoredItems = () => {
		return JSON.parse(localStorage.getItem(listName))
	}

	const retrieveStoredOptions = () => {
		return JSON.parse(localStorage.getItem(`${listName}Options`))
	}

	const syncItems = () => {
		localStorage.setItem(listName, JSON.stringify(items))
		localStorage.setItem(`${listName}Options`, JSON.stringify(options))
		items = retrieveStoredItems()
		options = retrieveStoredOptions()
	}

	const handleCheck = id => {
		items = items.map(item => item.id === id ? { ...item, checked: !item.checked } : item)
		syncItems()
	}

	const deleteItem = id => {
		items = items.filter(item => item.id !== id)
		syncItems()
	}

	const addNewItem = () => {
		if (!newItem) return
		items.push({
			name: newItem,
			id: Date.now(),
			checked: false
		})
		newItem = ''
		syncItems()
	}

	const checkAllItems = () => {
		items = items.map(item => ({...item, checked: true }))
		syncItems()
	}

	const uncheckAllItems = () => {
		items = items.map(item => ({...item, checked: false }))
		syncItems()
	}

	const deleteAllItems = () => {
		const confirmation = confirm('Are you sure you want to delete all items?')

		if(confirmation) {
			items = []
			syncItems()
		}
	}

	let existingItems = retrieveStoredItems()
	let existingOptions = retrieveStoredOptions()
	let items = existingItems || []
	let options = existingOptions || {
		sortByComplete: false,
		hideComplete: false
	}
	let newItem = ''

	$: filteredItems = items
		.sort((a, b) => options.sortByComplete ? a.checked > b.checked : a.id < b.id)
		.filter(item => options.hideComplete ? !item.checked : item)
	$: allAreChecked = (items.length && items.length === items.filter(item => item.checked).length)
</script>

<main>
	<div class="container">
		<h1>Svelte To-Do List</h1>
		<p><i>This is a to-do list made with Svelte, using browser <code>localStorage</code> to save your list in your browser.</i></p>

		<form on:submit|preventDefault={addNewItem}>
			<div>
				<input id="sort-by-completed" type="checkbox" bind:checked={options.sortByComplete} disabled={options.hideComplete} on:change={syncItems}>
				<label for="sort-by-completed">Send completed tasks to bottom</label>
			</div>
			<div>
				<input id="hide-completed" type="checkbox" bind:checked={options.hideComplete} on:change={syncItems}>
				<label for="hide-completed">Hide completed tasks</label>
			</div>
			<input type="text" bind:value={newItem} />
			<button type="submit">Add item</button>
		</form>
		{#if allAreChecked}
			<button on:click={uncheckAllItems}>⬜ Uncheck all</button>
		{:else}
			<button on:click={checkAllItems}>✅ Check all</button>
		{/if}
			<button on:click={deleteAllItems}>❌ Delete all</button>


		<hr>

		<ToDoList items={filteredItems} {handleCheck} {deleteItem} />

	</div>
</main>

<style>
hr, form {
	margin: 2rem 0;
}
</style>
