<script>
	import Lista from './listar.svelte';
	import Informacion from './informacion.svelte';
	let table = -1;

	function hacerAlgo(tableId) {
		table = tableId;
	}
</script>

<Lista callback={hacerAlgo} />
<Informacion tableId={table} />
