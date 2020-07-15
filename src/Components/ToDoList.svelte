<script>
	import ToDoItem from './ToDoItem.svelte'
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate'
	import { quintOut } from 'svelte/easing';


	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} translateY(${t});
					opacity: ${t}
				`
			};
		}
	});

	export let items
	export let handleCheck
	export let deleteItem
</script>



<ul>
	{#each items as item (item.id)}
		<li
			in:receive="{{key: item.id}}"
			out:send="{{key: item.id}}"
			animate:flip="{{duration: 400}}"
		>
			<ToDoItem
				name={item.name}
				id={item.id}
				checked={item.checked}
				{handleCheck}
				{deleteItem}
			/>
		</li>
	{/each}
</ul>



<style>
	ul {
		list-style-type: none;
		padding-left: 0;
	}

	li {
		display: grid;
		justify-content: space-between;
		align-items: stretch;
		padding: .5em;
		position: relative;
		line-height: 1.5;
		grid-template-columns: 1fr 1em;
		grid-gap: .5em;
	}

	li:nth-of-type(odd) {
		background: #f8f8f8;
	}

	li:hover {
		background: #eee;
	}
</style>

