<script>
	let users = [
		{ id: '1', name: 'Tanaka' },
		{ id: '2', name: 'Suzuki' },
		{ id: '3', name: 'Sato' }
	];
</script>

<ul>
	{#each users as user}
		<li>{user.name}</li>
	{/each}
</ul>
