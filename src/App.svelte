
<script>
	export let name;
	import Button from "./Button.svelte";
  import Valitse from "./Valitse.svelte";
let naytaNappi;
let selected;
let ingredients = [];
let shoppinglist = [];
</script>


<main>
	<header>
	<h1>{name}</h1></header>
	
<Valitse bind:naytaNappi bind:selected>
	
</Valitse>
	<div id="nappi">
	<Button bind:naytaNappi bind:selected bind:ingredients bind:shoppinglist>
		Generoi resepti
	</Button>

</div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		width: 70%;
		margin: 0 auto;
	}

	h1 {
		color: black;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 500;
	}

	header {
		background-color: greenyellow;
		height: 10em;
		text-align: center;
		border-style: solid;
  border-color: rgb(71, 32, 115);
	}
	#nappi {
	margin: 2em;
}
	@media (max-width: 640px) {
		main {
			max-width: none;
		}
		
	h1 {
		color: black;
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 4em;
	}
	header {
		background-color: greenyellow;
		height: 5em;
		text-align: center;
		border-style: solid;
  border-color: rgb(71, 32, 115);
	}
	}
</style>