<script>
	import axios from 'axios'; // Importa axios
	let tableId;
	export let tableStore;
	export let clientStore;

	let clientInfo = {
		dni: '',
		lastName: '',
		firstName: ''
	};

	tableStore.subscribe((value) => {
		tableId = value;
	});

	clientStore.subscribe((value) => {
		axios
			.get('http://localhost:8080/api/client?dni=' + value)
			.then((response) => {
				if (response.status == 200) {
					clientInfo = response.data;
				} else {
					clientInfo.firstName = '';
					clientInfo.lastName = '';
					clientInfo.dni = '';
				}
			})
			.catch(() => {
				console.error('Error updating client info:');
			});
	});

	async function updateClientInfo(event) {
		clientStore.set(clientInfo.dni);
		try {
			const response = await axios.get(
				'http://localhost:8080/api/client?dni=' + event.target.value
			);
			if (response.status == 200) {
				clientInfo = response.data;
			} else {
				clientInfo.firstName = '';
				clientInfo.lastName = '';
			}
		} catch (error) {
			console.error('Error updating client info:');
		}
	}

	async function changeClient(event) {
		let response = await axios.get('http://localhost:8080/api/client?dni=' + clientInfo.dni);
		console.log(clientInfo);

		if (response.status !== 200) {
			const registro = await axios.post('http://localhost:8080/api/client', {
				firstName: clientInfo.firstName,
				lastName: clientInfo.lastName,
				dni: clientInfo.dni
			});

			if (registro.status !== 200) {
				alert('No se puede registrar el usuario');
				return;
			}
		}

		try {
			response = await axios.put('http://localhost:8080/api/consumos/' + tableId, {
				cliente: clientInfo.dni
			});

			if (response.status !== 200) {
				alert('No se puede cambiar el cliente');
				return;
			}
		} catch (error) {
			alert('No se puede cambiar el cliente');
			console.log(error);
		}
	}
</script>

<div>
	<form on:submit|preventDefault={changeClient}>
		<label>Cliente:</label>
		<input
			type="text"
			bind:value={clientInfo.dni}
			on:input={updateClientInfo}
			placeholder="ID del Cliente"
		/>
		<label>
			Nombre del cliente
			<input type="text" name="nombreCliente" bind:value={clientInfo.firstName} required />
		</label>

		<label>
			Apellido del cliente
			<input type="text" name="apellidoCliente" bind:value={clientInfo.lastName} required />
		</label>
		<button type="submit">Cambiar cliente</button>
	</form>
</div>

<style>
	body {
		font-family: Arial, sans-serif;
	}

	form {
		display: flex;
		flex-direction: column;
		align-items: start;
		gap: 10px;
		margin-top: 20px;
	}

	label {
		margin-bottom: 5px;
	}

	input[type='text'] {
		padding: 8px;
		border: 1px solid #ccc;
		border-radius: 4px;
		width: 100%;
	}

	button {
		background-color: #4caf50;
		color: white;
		padding: 10px 20px;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		font-size: 16px;
	}

	button:hover {
		background-color: #45a049;
	}
</style>
