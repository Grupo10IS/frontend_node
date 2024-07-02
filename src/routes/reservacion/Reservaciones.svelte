<script>
    import axios from "axios";

    const url = "http://localhost:8080/api/";

    // Estado local para los datos del formulario
    let formData = {
        // los necesarios para la reservacion
        // NOTE: no cambiar el nombre
        tableId: "",
        restaurantId: "",
        date: "",
        reservationStart: "",
        reservationEnd: "",
        clientDni: "",

        // extras
        capacidad: "",
        nombreCliente: "",
        apellidoCliente: "",
    };

    // -- Estado local para las reservaciones --
    let reservaciones = [];

    // Función para manejar el envío del formulario
    const handleSubmit = async () => {
        try {
            const response = await axios.get(
                url +
                    `reservations?date=${formData.date}&resId=${formData.resId}&client=${formData.clientDni}`,
            );
            reservaciones = response.data;
        } catch (error) {
            alert("Hubo un error al listar las reserva");
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
            console.error("Error al listar los restaurantes:", error);
            alert("Error al listar las mesas");
        }
    };
</script>

<div>
    <form on:submit|preventDefault={handleSubmit}>
        <label on:focusin={listarRestaurantes}>
            Restaurante:
            <select bind:value={formData.restaurantId} required>
                <option value="" disabled selected
                    >Seleccion un restaurante</option
                >
                {#each restaurantes as restaurante}
                    <option value={restaurante.id}>
                        ID: {restaurante.id} Nombre: {restaurante.name}
                    </option>
                {/each}
            </select>
        </label>

        <label>
            Fecha:
            <input
                type="date"
                name="date"
                bind:value={formData.date}
                required
            />
        </label>

        <label>
            Cliente ID:
            <input
                type="text"
                name="dniCliente"
                bind:value={formData.clientDni}
            />
        </label>

        <button type="submit">Buscar</button>
    </form>

    <div>
        <ul>
            <h2>Lista de reservaciones</h2>
            {#each reservaciones as reserva}
                <li>
                    ID: {reserva.id} - DNI: {reserva.client} - Restaurante: {reserva.resId}
                    - Mesa:{reserva.table}
                    Inicio: {reserva.reservation_start} Fin: {reserva.reservation_end}
                    Fecha:
                    {reserva.date}
                </li>
            {/each}
        </ul>
    </div>
</div>

<style>
    h2 {
        text-align: center;
        margin-bottom: 20px;
    }

    /* Estilo para el formulario */
    form {
        display: flex;
        flex-direction: row;
        max-width: 500px;
        padding: 20px;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    label {
        margin-bottom: 10px;
    }

    input[type="date"],
    input[type="text"],
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
        height: 45px;
        margin: auto;
        margin-left: 5px;
        cursor: pointer;
        border: none;
        border-radius: 5px;
    }

    button:hover {
        background-color: #0056b3;
    }

    /* Estilo para la lista de reservaciones */
    ul {
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
</style>
