<script>
  import { onMount } from "svelte"; 
  
  export let naytaNappi;
  let categories = [];

  export let selected = '';

onMount (async () => {
  let data = await fetch('https://www.themealdb.com/api/json/v1/1/categories.php');
		let recipeData = await data.json();
		categories = [...recipeData.categories];
   
	});
  
</script>

<main>
<div class="dropdown">
  <label for="categories">Choose categorie:</label>
  <select id="categories"  name="categories" bind:value={selected} on:change="{() =>  naytaNappi = true}" >
    {#each categories as name}
    <option id="ohjenimi" >{name.strCategory}</option>
    {/each}
  </select>
</div>
</main>


<style>

  .dropdown{
    background-color: rgb(183, 183, 181);
    margin: 1em;
    padding: 1em;
    border-style: solid;
    border-color: black;
    
    
  }
	label {
    font-size: 2em;
    font-family: Arial, Helvetica, sans-serif;
  }

 
</style>