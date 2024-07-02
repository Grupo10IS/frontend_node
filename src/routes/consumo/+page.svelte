<script>
	import SelMesa from './restaurante_mesa.svelte';
	import InfoCliente from './cliente.svelte';
	import InfoConsumo from './consumision.svelte';
	import Productos from './productos.svelte';
	import Detalles from './detalles.svelte';
	import axios from 'axios';
	import { writable } from 'svelte/store';

	let table = writable(-1);
	let tbId = -1;
	table.subscribe((value) => {
		tbId = value;
	});

	export const cliente = writable(-1);
	let clientId = -1;
	cliente.subscribe((value) => {
		clientId = value;
	});

	let prodStore = writable(-1);
	let cantStore = writable(-1);
	let consStore = writable(-1);

	let showInfoConsumo = false;

	async function changeMesaCallback(tableId) {
		table.set(tableId);

		// traer el cliente de la mesa
		try {
			const response = await axios.get('http://localhost:8080/api/consumos/' + tableId);
			if (response.status == 200) {
				cliente.set(response.data.cliente);
			}
		} catch (error) {
			console.log(error);
		}

		showInfoConsumo = true;
	}

	async function crearConsumo() {
		try {
			const response = await axios.post('http://localhost:8080/api/consumos', {
				mesa: tbId,
				cliente: clientId
			});
			if (response.status !== 200) {
				alert('No se puede crear el consumo para esta mesa');
			} else {
				alert('Nueva consumision creada');
			}
		} catch (_) {
			alert('No se puede crear el consumo para esta mesa');
		}
	}

	async function prodInfo(id, cantidad) {
		prodStore.set(id);
		cantStore.set(cantidad);
	}
</script>

<div class="container">
	<SelMesa callback={changeMesaCallback} />

	{#if showInfoConsumo}
		<InfoCliente tableStore={table} clientStore={cliente} />
		<button on:click={crearConsumo}>Nueva consumision</button>
		<InfoConsumo tableStore={table} {prodStore} {cantStore} consumptionStore={consStore} />
		<br />
		<Productos callback={prodInfo} />
		<Detalles store={consStore} />
	{/if}
</div>

<style>
	.container {
		max-width: 800px;
		margin: auto;
		padding: 20px;
	}

	button {
		background-color: #4caf50; /* Verde */
		color: white; /* Texto blanco */
		padding: 15px 32px; /* Espaciado interno */
		text-align: center; /* Alineación del texto */
		text-decoration: none; /* Sin subrayado */
		display: inline-block; /* Mostrar como bloque inline */
		font-size: 16px; /* Tamaño de fuente */
		margin: 4px 2px; /* Margen alrededor */
		cursor: pointer; /* Cursor de puntero cuando se pasa por encima */
		border: none; /* Sin borde */
		border-radius: 4px; /* Bordes redondeados */
	}

	button:hover {
		background-color: #45a049; /* Oscuro cuando se pasa por encima */
	}
</style>
