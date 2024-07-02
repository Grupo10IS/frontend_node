<script>
	import { onMount } from 'svelte';
	import { createEventDispatcher } from 'svelte';
	import axios from 'axios'; // Importa axios

	export let tableId; // Propiedad reactiva para el ID de la mesa

	let clientInfo = {};
	let consumptionDetails = [];
	let newProduct = '';

	async function fetchConsumptionData() {
		try {
			const response = await axios.get(`http://localhost:8080/api/consumo?tableId=${tableId}`);
			clientInfo = response.data.client; // Asigna el cliente obtenido
			consumptionDetails = response.data.detalles; // Asigna los detalles de consumo
		} catch (error) {
			console.error('Error fetching consumption data:', error.message);
		}
	}

	async function fetchClientInfo(dni) {
		try {
			const response = await axios.get(`http://localhost:8080/api/client?dni=${dni}`);
			clientInfo = response.data;
		} catch (error) {
			console.error('Error fetching client info:', error.message);
		}
	}

	async function fetchProductInfo(productId) {
		try {
			const response = await axios.get(`http://localhost:8080/api/productos/${productId}`);
			return response.data; // Retorna el producto obtenido
		} catch (error) {
			console.error('Error fetching product info:', error.message);
			return null;
		}
	}

	async function fetchProducts() {
		try {
			const response = await axios.get('http://localhost:8080/api/products');
			return response.data; // Retorna la lista completa de productos
		} catch (error) {
			console.error('Error fetching products:', error.message);
			return [];
		}
	}

	onMount(async () => {
		await fetchConsumptionData();
	});

	function handleNewProductChange(event) {
		newProduct = event.target.value;
	}

	function addNewProduct() {
		// Implementa la lógica para agregar un nuevo producto a los detalles de consumo
		console.log('Agregar producto:', newProduct);
	}

	async function updateClientInfo(event) {
		try {
			const clResponse = await axios.get(
				'http://localhost:8080/api/client?dni=' + event.target.value
			);
			clientInfo = clResponse.data;
		} catch (error) {
			console.error('Error updating client info:', error.message);
		}
	}
</script>

<div>
	<label>Cliente:</label>
	<input
		type="text"
		bind:value={newProduct}
		on:input={updateClientInfo}
		placeholder="ID del Cliente"
	/>
	<div>Información del Cliente: {clientInfo.name}</div>

	<h2>Detalles de Consumo:</h2>
	<ul>
		{#each consumptionDetails as detail (detail.idProducto)}
			<li>
				ID Producto: {detail.idProducto}, Cantidad: {detail.cantidad}, Detalle: {detail.descripcion}
			</li>
		{/each}
	</ul>

	<button on:click={addNewProduct}>Agregar Nuevo Producto</button>
</div>
