<script>
	import axios from 'axios';
	import Reservaciones from './Reservaciones.svelte';

	const url = 'http://localhost:8080/api/';

	// Estado local para los datos del formulario
	let formData = {
		// los necesarios para la reservacion
		// NOTE: no cambiar el nombre
		tableId: '',
		restaurantId: '',
		date: '',
		reservationStart: '',
		reservationEnd: '',
		clientDni: '',

		// extras
		capacidad: '',
		nombreCliente: '',
		apellidoCliente: ''
	};
	// -- traer el nombre del cliente --
	const buscarCliente = async () => {
		try {
			const response = await axios.get(url + 'client?dni=' + formData.clientDni);
			if (response.status === 200) {
				formData.nombreCliente = response.data.firstName;
				formData.apellidoCliente = response.data.lastName;
			}
		} catch (error) {
			console.error('Error al buscar el cliente:', error);
			alert('Error al buscar el cliente');
		}
	};

	// -- Listar mesas disponibles --
	let mesas = [];

	const listarMesas = async () => {
		try {
			const response = await axios.get(
				url +
					`reservations/tables?resId=${formData.restaurantId}&date=${formData.date}&start=${formData.reservationStart}&end=${formData.reservationEnd}&capacity=${formData.capacidad}`
			);
			if (response.status === 200) {
				mesas = response.data; // Asigna las mesas al estado local
			}
		} catch (error) {
			console.error('Error al listar las mesas:', error);
			alert('Error al listar las mesas');
		}
	};

	// -- Listar restaurantes disponibles --
	let restaurantes = [];

	const listarRestaurantes = async () => {
		try {
			const response = await axios.get(url + `restaurant`);
			if (response.status === 200) {
				restaurantes = response.data; // Asigna las mesas al estado local
			}
		} catch (error) {
			console.error('Error al listar los restaurantes:', error);
			alert('Error al listar las mesas');
		}
	};

	// Función para manejar el envío del formulario
	const handleSubmit = async () => {
		// revisar que el cliente exista y registrarlo de ser necesario
		const response = await axios.get(url + 'client?dni=' + formData.clientDni);

		if (response.status !== 200) {
			const registro = await axios.post(url + 'client', {
				firstName: formData.nombreCliente,
				lastName: formData.apellidoCliente,
				dni: formData.clientDni
			});

			if (registro.status !== 200) {
				alert('No se puede registrar el usuario');
				return;
			}
		}

		try {
			const response = await axios.post(url + 'reservations', formData);
			if (response.status === 200) {
				alert('Reserva realizada exitosamente');
				// Aquí podrías manejar la redirección o actualizar la interfaz según necesites
			} else {
				alert('Hubo un error al realizar la reserva');
			}
		} catch (error) {
			alert('Hubo un error al realizar la reserva');
		}
	};
</script>

<div class="center">
	<form class="formulario-reservas" on:submit|preventDefault={handleSubmit}>
		<h1>Formulario de Reserva de Mesa</h1>
		<label on:focusin={listarRestaurantes}>
			Restaurante:
			<select bind:value={formData.restaurantId} required>
				<option value="" disabled selected>Seleccion un restaurante</option>
				{#each restaurantes as restaurante}
					<option value={restaurante.id}>
						ID: {restaurante.id} Nombre: {restaurante.name}
					</option>
				{/each}
			</select>
		</label>

		<label>
			Fecha:
			<input type="date" name="date" bind:value={formData.date} required />
		</label>

		<label>
			Hora inicio:
			<input type="number" name="inicio" bind:value={formData.reservationStart} required />
		</label>

		<label>
			Hora fin:
			<input type="text" name="final" bind:value={formData.reservationEnd} required />
		</label>

		<label>
			Capacidad:
			<input
				type="number"
				name="capacidad"
				on:change={listarMesas}
				bind:value={formData.capacidad}
				required
			/>
		</label>

		<label>
			Cliente ID:
			<input
				type="text"
				name="dniCliente"
				bind:value={formData.clientDni}
				on:change={buscarCliente}
				required
			/>
		</label>

		<label>
			Mesa:
			<select bind:value={formData.tableId} required>
				<option value="" disabled selected>Seleccione una mesa</option>
				{#each mesas as mesa}
					<option value={mesa.id}>
						ID: {mesa.id} - Capacidad: {mesa.capacity} - Pos: ({mesa.posX},
						{mesa.posY})
					</option>
				{/each}
			</select>
		</label>

		<label>
			Nombre del cliente
			<input type="text" name="nombreCliente" bind:value={formData.nombreCliente} required />
		</label>

		<label>
			Apellido del cliente
			<input type="text" name="apellidoCliente" bind:value={formData.apellidoCliente} required />
		</label>
		<button type="submit">Reservar Mesa</button>
	</form>

	<Reservaciones />
</div>

<style>
	h2 {
		text-align: center;
		margin-bottom: 20px;
	}

	/* Contenedor principal */
	div {
		display: flex;
		justify-content: space-between;
		align-items: flex-start;
		gap: 20px; /* Espacio entre las columnas */
		padding: 20px;
	}

	/* Estilo para el formulario */
	form {
		display: flex;
		align-items: start;
		flex-direction: column;
		max-width: 500px;
		padding: 20px;
		border-radius: 8px;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
		align-items: left;
	}

	label {
		margin-bottom: 10px;
	}

	input[type='date'],
	input[type='text'],
	select {
		padding: 8px;
		font-size: 16px;
		border: 1px solid #ccc;
		border-radius: 4px;
		outline: none;
		margin-bottom: 10px;
	}

	button {
		background-color: #007bff;
		color: white;
		padding: 10px 20px;
		font-size: 18px;
		cursor: pointer;
		border: none;
		border-radius: 5px;
		margin: auto;
	}

	button:hover {
		background-color: #0056b3;
	}

	/* Estilo para la lista de reservaciones */
	Reservaciones {
		flex: 1; /* Toma el espacio disponible */
		list-style-type: none;
		padding: 0;
	}

	li {
		background-color: #333333;
		padding: 15px;
		border-radius: 5px;
		margin-bottom: 10px;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
	}

	li:last-child {
		margin-bottom: 0;
	}

	.center {
		margin-left: 150px;
		margin-right: 200px;
	}
</style>
