<script>
	import { onMount } from 'svelte';
	import axios from 'axios';

	export let store;
	let consumptionId = -1;
	let consumptionDetails = [];
	let totalSum = 0;

	store.subscribe((value) => {
		consumptionId = value;
		fetchConsumptionDetails();
	});

	async function fetchProductDetails(productId) {
		try {
			const response = await axios.get('http://localhost:8080/api/productos/' + productId);
			if (response.status === 200) {
				return response.data;
			}
		} catch (error) {
			console.error('Error fetching product details:', error);
		}
		return null;
	}

	async function fetchConsumptionDetails() {
		try {
			const response = await axios.get('http://localhost:8080/api/detalles/' + consumptionId);
			if (response.status === 200) {
				const details = response.data;
				const productPromises = details.map(async (detail) => {
					const productInfo = await fetchProductDetails(detail.producto);
					return {
						...detail,
						info: productInfo
					};
				});
				consumptionDetails = await Promise.all(productPromises);
				calculateTotalSum();
			} else {
				consumptionDetails = [];
			}
		} catch (error) {
			console.error('Error fetching consumption details:', error);
			consumptionDetails = [];
		}
	}

	function calculateTotalSum() {
		totalSum = consumptionDetails.reduce((sum, detail) => {
			return sum + (detail.info ? detail.info.precio * detail.cantidad : 0);
		}, 0);
	}

	onMount(() => {
		fetchConsumptionDetails();
	});

	import jsPDF from 'jspdf';
	import html2canvas from 'html2canvas';

	let elementToConvert;

	async function generatePdf() {
		const canvas = await html2canvas(elementToConvert);
		const imgData = canvas.toDataURL('image/png');

		const doc = new jsPDF();
		doc.addImage(imgData, 'PNG', 10, 10);
		doc.save('download.pdf');
	}
</script>

<div bind:this={elementToConvert}>
	<div>
		<h2>Detalles de Consumición</h2>
		<ul>
			{#each consumptionDetails as detail}
				<li>
					<strong>Producto ID:</strong>
					{detail.producto}<br />
					<strong>Detalles:</strong>
					<ul>
						<li>Descripción: {detail.info ? detail.info.name : 'No disponible'}</li>
						<li>Precio: {detail.info ? detail.info.precio : 'No disponible'}</li>
					</ul>
					<strong>Cantidad:</strong>
					{detail.cantidad}<br />
				</li>
			{/each}
		</ul>
		Total: {totalSum}

		<br />
		<button on:click={generatePdf}>Generar PDF</button>
	</div>
</div>

<style>
	body {
		font-family: Arial, sans-serif;
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

	strong {
		font-weight: bold;
	}

	h3 {
		color: #666;
		margin-top: 20px;
	}
</style>
