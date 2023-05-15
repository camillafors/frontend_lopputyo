<script>

let naytaGeneroituResepti = false;
export let naytaNappi = false;
export let selected = '';
let recipes = [];
export let ingredients = [];
let showShoppinglist = false;
 
class Ingredient { 
  constructor(name, amount) {
    this.name = name;
    this.amount = amount;
  }
}


export let shoppinglist = [];
const errMessage = "Kaikki kentät ovat pakollisia";	

let sposti = "";
	let spostiVierailtu = false;
  let validRegex =/^[^\s@]+@[^\s@]+\.[^\s@]+$/;
$: spostiKelpaa = sposti.length > 0 && sposti.match(validRegex);
	$: disabled = !(spostiKelpaa);

 let onkoReseptiNaytetty = false



//näyttää koko reseptin ajamalla tiedot funktion ja asettamalla naytaGeneroituResepti trueksi
//ajaa reset funktion sen jälkeen, jotta kaikki tarvittavat kentät ja muuttujat tyhjennetään uudelleen painamista varten
function generoi(){
    naytaGeneroituResepti= true
    tiedot();
    onkoReseptiNaytetty = true
    reset();
}

//valitsee satunnaisesti jonkin numeron reseptien pituudesta ja asettaa sen valittuu reseptiin
function valitseId (){
    let randomValinta = Math.floor(Math.random() * recipes.length);
    
    let valittuResepti = recipes[randomValinta];
    
    return valittuResepti.idMeal
    
  }
	
  
  async function tiedot () {
    //hakee valitse.sveltestä selected arvon ja hakee tietyn kategorian apista
    let kutsuUrl = (`https://www.themealdb.com/api/json/v1/1/filter.php?c=${selected}`)
  let data = await fetch(kutsuUrl);
  let chosenMeals = await data.json();
		recipes = [...chosenMeals.meals];

    // hakee valitun id:n perusteella tiedot apista.
   let valittuId = valitseId();
   kutsuUrl = (`https://www.themealdb.com/api/json/v1/1/lookup.php?i=${valittuId}`)
   data = await fetch(kutsuUrl);
   chosenMeals = await data.json();
		recipes = [...chosenMeals.meals];
    
   luoSiirtyma();
	};

 
  //laittaa reseptit listaan ja palauttaa sen
function recipeFieldsToList(recipe){
  const strIngredient = 'strIngredient'
  const strMeasure = 'strMeasure'

   ingredients = [];
  for (let i = 1; i < 21; i++) {

  let strIngredientN=strIngredient+i
  let strMeasureN=strMeasure+i
  let ingredient = recipe[strIngredientN]
  let measure = recipe[strMeasureN]
  const incci = new Ingredient(ingredient, measure);


    if (ingredient === '') {
      return ingredients
      }
    
      if (ingredient === null) {
        console.log('Null löydetty')
        continue
      }
      ingredients.push(incci);
  } 

return ingredients
}

// tekee siirtymän kohtaan id:n perusteella  
function luoSiirtyma (){
const generoiNappi= document.getElementById('generoinappi');
generoiNappi.scrollIntoView({ behavior: 'smooth' });

}

// luo kauppalistan reseptin ainesosista looppaamalla onko ainesosa jo tarkistettu ja palauttaa lopuksi showShoppinglistin truena
function generateShoppinglist (){
  
  shoppinglist = [];
for (let i = 0; i < ingredients.length; i++) {
 let cb = document.getElementById(`ingredient_cb_${ingredients[i].name}`)
 
 if(!cb.checked) {
  shoppinglist.push(ingredients[i])
 }
}
showShoppinglist = true
}
//näyttää alert ikkunan ja siellä halutun viestin
function showConfirmation(){
  alert("The shopping list has been sent successfully!");
  console.log(shoppinglist);
}

//resetoi checkboxit ja shoppinglistin
function reset (){
  if (onkoReseptiNaytetty === true) {
    const checkboxes = document.querySelectorAll('input[type="checkbox"]');
    checkboxes.forEach((checkbox) => {
      checkbox.checked = false;
    });
  shoppinglist = [] ;
}
}

</script>
<main>

  {#if naytaNappi}
<button id="generoinappi" on:click={generoi} >Generate recipe</button>
{#if naytaGeneroituResepti}

	<h2 id="otsikko">Here is recipe</h2>
  {#each recipes as name}
  <p id="ohjenimi">{name.strMeal}</p>
  {/each}
  {#each recipes as recipe}
    <img id="meal" src="{recipe.strMealThumb}" alt="meal" >

    <div id="laatikot">
    
    <div id="laatikko1"> 
      <p class="teksti">Ingredients</p>
      <p class="teksti2">Click what you have in the fridge</p>
    {#each recipeFieldsToList(recipe) as ingredient}
    
      <input  type="checkbox" id={`ingredient_cb_${ingredient.name}`}> {ingredient.amount} {ingredient.name} <br>
    {/each}
  </div>
 <div id="laatikko2">
 <p class="teksti">Instruction</p> 
  
    <p class="ohje">{recipe.strInstructions}</p>
  </div>
  <div id="shoppinglist">
    <button id="generoilista" on:click={generateShoppinglist}>Generate shoppinglist</button>
     {#if showShoppinglist} 
      {#each shoppinglist as product}
       <p id="lista">{product.name} {product.amount}</p>
      {/each}
      <input name="text1" bind:value={sposti} type="text" on:blur={() => (spostiVierailtu = true)}>
       {#if spostiVierailtu && !spostiKelpaa}
	      <p class="error-alert">{errMessage}</p>
       {/if}
        <button disabled={disabled} on:click={showConfirmation}>Send shoppinglist</button>
       {/if}
   </div>
  </div>
  {/each}

	{/if}
  {/if}
</main>

<style>

#generoinappi {
  
  background-color:rgb(56, 56, 56) ;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  
}

#generoilista {
  background-color:rgb(56, 56, 56) ;
  border: none;
  color: white;
  padding: 0.5em 2em;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
}

.teksti {
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 1.5em;
  text-align: left;
  
} 

#ainekset {
  font-weight: bold;
  text-align: left;
}

.ohje {
  text-align: left;
}

#id {
  text-align: right;
}

#laatikko1 {
  width: 40%;
  float: left;
  text-align: left;
}

#laatikko2 {
  width: 40%;
  float: left
}

#shoppinglist {
  width: 20%;
  float: right;
  
}

#ohjenimi {
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 1.5em;
}

#laatikot {
  flex-direction: row;
}
#otsikko {
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 1.5em;
}
#meal {
  width:30%;
  height:40%;
}

.teksti2 {
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 1em;
  text-align: left;
  
}
@media (max-width: 640px) {
	

    #laatikot {
      flex-direction: column;
    }
    #laatikko1, #laatikko2 {
    width: 100%;
    float: none;
  }
  #shoppinglist {
    width: 100%;
    float: none;
  }
  #meal {
  width:80%;
  height:90%;
}
 
	}
</style>