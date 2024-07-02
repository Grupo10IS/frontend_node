<script>
	import axios from 'axios'; // Importa axios
	export let tableStore;
	export let prodStore;
	export let cantStore;
	export let consumptionStore;

	let tableId;

	let prodId;
	let cantidad;

	let consumptionInfo = {
		id: '',
		mesa: '',
		cliente: '',
		fecha_apertura: ''
	};

	tableStore.subscribe((value) => {
		tableId = value;
		fetchConsumptionDetails();
	});

	prodStore.subscribe((value) => {
		prodId = value;
	});

	cantStore.subscribe((value) => {
		cantidad = value;
	});

	async function fetchConsumptionDetails() {
		try {
			const response = await axios.get('http://localhost:8080/api/consumos/' + tableId);
			if (response.status === 200) {
				consumptionInfo = response.data;
				consumptionStore.set(consumptionInfo.id);
			} else {
				consumptionInfo.id = '';
				consumptionInfo.mesa = '';
				consumptionInfo.cliente = '';
				consumptionInfo.fecha_apertura = '';
			}
		} catch (_) {
			consumptionInfo.id = '';
			consumptionInfo.mesa = '';
			consumptionInfo.cliente = '';
			consumptionInfo.fecha_apertura = '';
			consumptionStore.set(consumptionInfo.id);
		}
	}

	async function add_roducto() {
		try {
			const response = await axios.post('http://localhost:8080/api/detalles', {
				consId: consumptionInfo.id,
				prodId: prodId,
				cantidad: cantidad
			});
			consumptionStore.set(consumptionInfo.id);
			alert('Succesfull');
		} catch (error) {
			console.log(error);
		}
	}
</script>

<div>
	<label>ID Consumision:</label>
	<input type="text" bind:value={consumptionInfo.id} readonly />
	<label>Mesa:</label>
	<input type="text" bind:value={consumptionInfo.mesa} readonly />
	<label>Fecha Apertura:</label>
	<input type="text" bind:value={consumptionInfo.fecha_apertura} readonly />

	<button on:click={add_roducto}> add producto </button>
</div>

<style>
	label {
		display: block; /* Cada etiqueta se muestra en su propia línea */
		margin-bottom: 5px; /* Espacio debajo de cada etiqueta */
	}

	input[type='text'] {
		width: 100%; /* Ancho completo del contenedor padre */
		padding: 8px; /* Espaciado interno */
		border: 1px solid #ccc; /* Borde sencillo */
		border-radius: 4px; /* Bordes redondeados */
	}

	button {
		background-color: #007bff; /* Color de fondo azul */
		color: white; /* Texto blanco */
		border: none; /* Sin borde */
		padding: 10px 20px; /* Espaciado interno */
		text-align: center; /* Alineación del texto */
		text-decoration: none; /* Sin subrayado */
		display: inline-block; /* Mostrar como bloque inline */
		font-size: 16px; /* Tamaño de fuente */
		margin: 4px 2px; /* Margen alrededor */
		cursor: pointer; /* Cursor de puntero cuando se pasa por encima */
		border-radius: 4px; /* Bordes redondeados */
	}
</style>
