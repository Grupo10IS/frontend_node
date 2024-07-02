<script>
	import { onMount } from 'svelte';

	// @ts-ignore
	export let callback;

	// @ts-ignore
	let productos = [];
	let selectedProdId = [];
	let cantidad = 0;

	async function fetchProductos() {
		const response = await fetch('http://localhost:8080/api/productos');
		if (response.ok) {
			productos = await response.json();
		} else {
			console.error('Error fetching restaurants:', response.statusText);
		}
	}

	// @ts-ignore
	function selectProd(event) {
		selectedProdId = event.target.value;
		callback(selectedProdId, cantidad);
	}

	function selectCantidad(event) {
		cantidad = event.target.value;
		callback(selectedProdId, cantidad);
	}

	onMount(() => {
		fetchProductos();
	});
</script>

<select on:change={selectProd}>
	<option value="">Seleccione un producto</option>
	{#each productos as prod (prod.prod_id)}
		<option value={prod.prod_id}>{prod.name}: {prod.precio}</option>
	{/each}
</select>
<label>
	Cantidad:
	<input type="number" name="capacidad" bind:value={cantidad} on:change={selectCantidad} />
</label>

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

	label {
		display: block;
		margin-top: 10px;
	}

	input[type='number'] {
		width: 100%;
		padding: 8px;
		border: 1px solid #ccc;
		border-radius: 4px;
		text-align: right;
	}
</style>
