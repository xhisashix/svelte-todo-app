<script>
// @ts-nocheck

	import { onMount } from 'svelte';

	let todoData = [{ id: Number, title: String }];
	let url = 'http://localhost:3000/todo';
	onMount(async () => {
		const res = await fetch(url);
		todoData = await res.json();
	});

	/**
	 * @param {string | any[]} todoList
	 */
	function getTodoLen(todoList) {
		return todoList.length + 1;
	}

	/**
	 * @type {string}
	 */
	let value;

	/**
	 * @param {any[]} todoList
	 */
	function addTodo(todoList) {
		todoData = todoList.concat({ id: getTodoLen(todoList), title: value });
		console.log(todoData);
		let putData = todoData[todoData.length - 1];
		putTodo('http://localhost:3000/todo', putData);
	}

	/**
	 * @param {string} url
	 * @param {any[]} data
	 */
	async function putTodo(url, data) {
		await fetch(url, {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify(data)
		})
			.then((response) => response.json())
			.then((data) => {
				console.log('Success', data);
			})
			.catch((error) => {
				console.log('Error', error);
			});
	}
</script>

<svelte:head>
	<title>Todo</title>
	<meta name="description" content="Svelte todo app" />
</svelte:head>

<section>
	<h1>Todo</h1>

	<input type="text" bind:value />
	<button on:click={addTodo(todoData)}>追加</button>

	<ul>
		{#each todoData as todo}
			<li>
				<p>{todo.id}</p>
				<input type="checkbox" />
				<p>{todo.title}</p>
				<p />
			</li>
		{/each}
	</ul>
</section>

<style>
	ul li {
		display: flex;
	}
</style>
