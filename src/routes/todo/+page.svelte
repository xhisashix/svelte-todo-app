<script>
	let todos = [{ id: 1, title: 'テスト' }];
	let todoTitle = '';
	function getTodoLen() {
		return todos.length + 1;
	}

	function add() {
		todos = todos.concat({ id: getTodoLen(), title: todoTitle });
		console.log(todos);
	}
</script>

<svelte:head>
	<title>Todo</title>
	<meta name="description" content="Svelte todo app" />
</svelte:head>

<section>
	<h1>Todo</h1>

	<input type="text" bind:value={todoTitle} />
	<button on:click={add}>追加</button>

	<ul>
		{#each todos as todo}
			<li>
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
