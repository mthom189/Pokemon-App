<script setup> 
import { ref, reactive } from "vue"; 
 
// replace var query = "bulbasaur"; 
var query = ref("bulbasaur"); 
//we are filling in some data to avoid errors in our browser 
const state = reactive({ 
  pokemon: { 
    name: {}, 
    height: {}, 
    weight: {}, 
    id: {}, 
  }, 
  species: {}, 
  genera: { genus: {} }, 
  flavor_text_entry: { flavor_text: {} }, 
}); 
 
var requestOptions = { 
  method: "GET", 
  redirect: "follow", 
}; 
 
fetchPokemon() 
function fetchPokemon() { 
  query.value = query.value.toLowerCase(); 
  if(query.value == "" || query.value == undefined || query.value == null || !isNaN(query.value)) {
    alert("Invalid request");
  } else {
      // make sure to replace query with query.value and the second .then 
  fetch(`https://pokeapi.co/api/v2/pokemon/${query.value}`, requestOptions) 
    .then((response) => { 
      return response.json(); 
    }) 
    .then(setResults) 
    .catch((error) => console.log("error", error)); 
  query.value = "";
  }
} 
 
function setResults(results) { 
  state.pokemon = results; 
  state.pokemon.sprite =  
  state.pokemon.sprites.front_shiny; 
  state.pokemon.height = (state.pokemon.height * 0.1).toFixed(1); 
  state.pokemon.weight = (state.pokemon.weight * 0.1).toFixed(1); 

  fetch(`${state.pokemon.species.url}`, requestOptions) 
    .then((response) => { 
      return response.json(); 
    }) 
    .then(setSpeciesInfo) 
    .catch((error) => console.log("error", error));  
} 

function setSpeciesInfo(speciesInfo) { 
  state.species = speciesInfo; 
  state.flavor_text_entry = state.species.flavor_text_entries.find(checkLanguage); 
  state.flavor_text_entry.flavor_text = 
  state.flavor_text_entry.flavor_text.replace("\f", " "); 
  state.genera = state.species.genera.find(checkLanguage); 
} 
 
function checkLanguage(language) { 
  if (language.language.name == "en") return language; 
} 
</script> 

<template>
  <div class="pokedex">
    <div class="topbar">
      <div class="title">
        <div class="number">
          <!--we replace our dummy data with actual data--> 
           <p>No. {{ state.pokemon.id }}</p> 
        </div>
        <!-- here we place our data pokemon name --> 
        <header>{{ state.pokemon.name }}</header> 
      </div> 
      <div class="species"> 
        <div class="genera">{{ state.genera.genus }}</div>
        <ul> 
          <!-- we are using a vue directive here which is saying 
          for every type in the state.pokemon.types array display a list item --> 
          <li v-for="type in state.pokemon.types" :key="type.type.name"> 
          {{ type.type.name.toUpperCase() }}     
          </li> 
        </ul> 
      </div> 
    </div> 
    <div class="main"> 
      <div class="image"><img :src="state.pokemon.sprite" /></div> 
      <div class="attributes"> 
        <!-- adding some pokemon attribute --> 
        <p class="stats">Weight: {{ state.pokemon.weight }} kg</p> 
        <!-- adding another pokemon attribute --> 
        <p class="stats">Height: {{ state.pokemon.height }} m</p> 
        <p class="description">{{ state.flavor_text_entry.flavor_text }}</p> 
      </div> 
    </div> 
    <div class="bottom"> 
      <input 
        v-model="query" 
        placeholder="Enter pokemon name or number" 
        @keyup.enter="fetchPokemon" 
      /> 
    </div> 
  </div> 
</template> 

<style>
@import "./assets/base.css";

#app {
  max-width: 1280px;
  min-width: 768px;
  margin: 0 auto;
  padding: 2rem;
  font-weight: normal;
}

.pokedex {
  display: flex;
  background-color: #e8e6cf;
  flex-direction: column;
}

.topbar {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  background-color: #324357;
  height: 50px;
  margin: 20px;
  color: #c3c9cf;
  border-radius: 10px;
  align-items: center;
  justify-content: space-around;
}

.main {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: space-around;
  background-color: #e8e6cf;
  margin: 10px 0px 20px 0px;
}

.attributes {
  display: flex;
  flex-direction: column;
  flex: 0 1 25%;
  background-color: #e8e6cf;
  color: #8a91a0;
  justify-content: center;
}

.stats {
  padding: 0px 0px 0px 15px;
}

.description {
  background-color: #faf6e3;
  margin: 10px;
  padding: 10px;
  color: #8a91a0;
  border-radius: 10px;
  flex-grow: 6;
}

.title {
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 5px 5px 5px 5px;
}

.species {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  align-items: center;
}
/* top right bottom left*/
.genera {
  border-radius: 5px;
  padding: 5px 30px 5px 30px;
  margin: 0px 10px 0px 10px;
  background-color: #485663;
  font-size: 1.2rem;
}

li {
  background-color: #343e3d;
  padding: 5px 5px;
  margin: 0px 5px 0px 5px;
  font-weight: 500;
}

.image {
  flex: 0 2 5% 5%;
  background-color: white;
}

header {
  font-size: 2rem;
  margin: 5px;
  font-weight: bold;
}

header::first-letter {
  text-transform: capitalize;
}

.number {
  font-size: 1.2rem;
  border: 2px solid #c3c9cf;
  border-radius: 10px;
  margin: 5px;
}

ul {
  list-style: none;
  display: flex;
  flex-direction: row;
}

.bottom {
  display: flex;
  justify-content: center;
  margin: 10px 0px 20px 0px;
}

input {
  padding: 10px 10px 10px 10px;
  font-size: 1rem;
  background-color: #485663;
  color: white;
}

::placeholder {
  color: #c3c9cf;
}
</style>
