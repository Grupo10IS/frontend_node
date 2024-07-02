<script>
	import { onMount } from 'svelte';

	// @ts-ignore
	export let callback;

	// @ts-ignore
	let restaurants = [];
	// @ts-ignore
	let selectedRestaurantId;
	// @ts-ignore
	let tables = [];
	// @ts-ignore
	let currentRestaurantId;

	async function fetchRestaurants() {
		const response = await fetch('http://localhost:8080/api/restaurant');
		if (response.ok) {
			restaurants = await response.json();
		} else {
			console.error('Error fetching restaurants:', response.statusText);
		}
	}

	async function fetchTables() {
		// @ts-ignore
		if (!currentRestaurantId) return; // No hay restaurante seleccionado
		const response = await fetch(`http://localhost:8080/api/tables?resId=${currentRestaurantId}`);
		if (response.ok) {
			tables = await response.json();
		} else {
			console.error('Error fetching tables:', response.statusText);
			tables = [];
		}
	}

	// @ts-ignore
	function selectRestaurant(event) {
		selectedRestaurantId = event.target.value;
	}

	// @ts-ignore
	function handleTableClick(tableId) {
		// Aquí puedes implementar la lógica que quieras ejecutar cuando se hace clic en una mesa
		console.log(`Mesa ${tableId} fue seleccionada`);
		callback(tableId);
	}

	// @ts-ignore
	$: if (selectedRestaurantId) {
		currentRestaurantId = selectedRestaurantId;
		fetchTables();
	} else {
		tables = [];
	}

	onMount(() => {
		fetchRestaurants();
	});
</script>

<select on:change={selectRestaurant}>
	<option value="">Seleccione un restaurante</option>
	{#each restaurants as restaurant (restaurant.id)}
		<option value={restaurant.id}>{restaurant.name}</option>
	{/each}
</select>

{#if tables.length > 0}
	<h2>Mesas disponibles:</h2>
	<ul>
		{#each tables as table (table.id)}
			<li>
				<button on:click={() => handleTableClick(table.id)}
					>{table.name} - Posición X: {table.posX}, Y: {table.posY}</button
				>
			</li>
		{/each}
	</ul>
{:else}
	<p>No hay mesas disponibles para el restaurante seleccionado.</p>
{/if}

<style>
	body {
		font-family: Arial, sans-serif;
	}

	select {
		width: 100%;
		padding: 8px;
		border: 1px solid #ccc;
		border-radius: 4px;
		appearance: none;
		-webkit-appearance: none;
		-moz-appearance: none;
	}

	option {
		padding: 6px 12px;
	}

	h2 {
		color: #333;
		margin-bottom: 10px;
	}

	ul {
		list-style-type: none;
		padding-left: 0;
	}

	li {
		margin-bottom: 10px;
		padding: 10px;
		border: 1px solid #ddd;
		border-radius: 4px;
	}

	button {
		width: 100%;
		padding: 8px;
		border: none;
		border-radius: 4px;
		background-color: #007bff;
		color: white;
		cursor: pointer;
		font-size: 16px;
	}

	button:hover {
		background-color: #0056b3;
	}
</style>
