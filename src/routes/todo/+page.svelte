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
		setTimeout(() => {
			getTodoData();
		}, 1000);
	}

	/**
	 * Un done todo
	*/
	function unDoneTodo(todoData) {
		todoData.done = false;
		updateTodo(todoData);
		setTimeout(() => {
			getTodoData();
		}, 1000);
	}

	/**
	 * change todo title
	 */
	function changeTodoTitle(todoData, value) {
		todoData.title = value;
		updateTodo(todoData);
		setTimeout(() => {
			getTodoData();
		}, 1000);
	}

	/**
	 * delete todo
	 */
	async function deleteTodoData(todoData) {
		await fetch(url + '/' + todoData.id, {
			method: 'DELETE'
		})
			.then((response) => {
				getTodoData();
			})
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
	<button disabled={!value} on:click={addTodo(todoData)}>追加</button>

	<h2 class="section-title">Task</h2>
	<ul>
		{#each todoData as todo}
			{#if todo.done === false}
				<li>
					<input bind:value={todo.title} />
					<button class="btn-done"  on:click={doneTodo(todo)}>Done</button>
					<button class="btn-edit" on:click={changeTodoTitle(todo, todo.title)}>Edit</button>
				</li>
			{/if}
		{/each}
	</ul>
	<h2 class="section-title">Done</h2>
	<ul>
		{#each todoData as todo}
			{#if todo.done === true}
				<li class="done">
					<p class="todo-list-item">{todo.title}</p>
					<button class="btn-un-done" on:click={unDoneTodo(todo)}>UnDone</button>
					<button class="btn-delete" on:click={deleteTodoData(todo)}>Delete</button>
				</li>
			{/if}
		{/each}
	</ul>
</section>

<style>
	ul li {
		display: flex;
		margin-top: 10px;
		align-items: center;
	}
	ul li input {
		width: 250px;
		margin-right: 20px;
		padding: 5px;
		border: #ccc 2px solid;
	}
	ul li button {
		margin-right: 20px;
		border-radius: 5px;
		box-sizing: border-box;
		padding: 5px 10px;
		width: 80px;
		height: 30px;
		box-shadow: 2px 2px 2px #ccc;
	}

	ul li button:hover {
		cursor: pointer;
		transform: rotate(5deg);
	}
	ul li .todo-list-item {
		min-width: 250px;
		margin-right: 20px;
		border: #ccc 2px solid;
		padding: 5px;
	}
	.btn-done {
		background-color: #4caf50;
		color: #fff;
		border: #4caf50 1px solid;
	}
	.btn-edit {
		background-color: #2196f3;
		color: #fff;
		border: #2196f3 1px solid;
	}
	.btn-un-done {
		background-color: #f44336;
		color: #fff;
		border: #f44336 1px solid;
	}
	.btn-delete {
		background-color: #4e4a4a;
		color: #fff;
		border: #4e4a4a 1px solid;
	}
	ul .done {
		opacity: 0.4;
	}
	.section-title {
		font-size: 24px;
		font-weight: bold;
	}
</style>
