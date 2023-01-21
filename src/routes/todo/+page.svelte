<script>
	// @ts-nocheck

	import { onMount } from 'svelte';

	let todoData = [{ id: Number, title: String, done: Boolean }];
	$: todoDatas = todoData;
	const url = 'http://localhost:3000/todo';
	onMount(async () => {
		getTodoData();
	});

	async function getTodoData() {
		const res = await fetch(url);
		todoData = await res.json();
	}

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
		todoData = todoList.concat({ id: getTodoLen(todoList), title: value, done: false });
		console.log(todoData);
		let postData = todoData[todoData.length - 1];
		postTodo(url, postData);
	}

	/**
	 * @param {string} url
	 * @param {any[]} data
	 */
	async function postTodo(url, data) {
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

	/**
	 * update Todo data
	 */
	async function updateTodo(todoData) {
		await fetch(url + '/' + todoData.id, {
			method: 'PUT',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({ id: todoData.id, title: todoData.title, done: todoData.done })
		})
			.then((response) => response.json())
			.then((data) => {
				console.log('Success', data);
			})
			.catch((error) => {
				console.log('Error', error);
			});
	}

	/**
	 * Done todo
	 */
	function doneTodo(todoData) {
		todoData.done = true;
		updateTodo(todoData);
		getTodoData()
	}

	/**
	 * Un done todo
	*/
	function unDoneTodo(todoData) {
		todoData.done = false;
		updateTodo(todoData);
		getTodoData()
	}

	/**
	 * change todo title
	 */
	function changeTodoTitle(todoData, value) {
		todoData.title = value;
		console.log(value)
		updateTodo(todoData);
		getTodoData()
	}
</script>

<svelte:head>
	<title>Todo</title>
	<meta name="description" content="Svelte todo app" />
</svelte:head>

<section>
	<h1>Todo</h1>
	<input type="text" bind:value />
	<button disabled={!value} on:click={addTodo(todoData)}>追加</button>

	<h2>Task</h2>
	<ul>
		{#each todoData as todo}
			{#if todo.done === false}
				<li>
					<input bind:value={todo.title} />
					<button on:click={doneTodo(todo)}>Done</button>
					<button on:click={changeTodoTitle(todo, todo.title)}>Edit</button>
				</li>
			{/if}
		{/each}
	</ul>
	<h2>Done</h2>
	<ul>
		{#each todoData as todo}
			{#if todo.done === true}
				<li class="done">
					<p>{todo.title}</p>
					<button on:click={unDoneTodo(todo)}>UnDone</button>
				</li>
			{/if}
		{/each}
	</ul>
</section>

<style>
	ul li {
		display: flex;
	}
	ul .done {
		opacity: 0.4;
	}
</style>
