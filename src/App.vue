<script setup>
  import { ref, reactive } from "vue";

  document.title="Pokemon Quiz";
  const state = reactive({
    pokemon: {
      name: {},
      id: {},
    },
  });
  var otherNames = reactive({
    pokemon: {
      name: {},
      id: {},
    },
  });
  //array for pokemon names
  var ansOptions = new reactive([]); 
  var randArray = new reactive([]); //randomized array

  //score tracker
  var score = 0;


  var requestOptions = {
    method: "GET",
    redirect: "follow",
  };


  fetchPokemon()
  //fetch random pokemon, populate array with random names, then shuffle array
  function fetchPokemon() {
      ansOptions.splice(0);
      randArray.splice(0);
      fetch(`https://pokeapi.co/api/v2/pokemon/${rng()}`.toLowerCase(), requestOptions)
      .then((response) => {
        return response.json();
      })
      .then(setResults)
      .then(randArray = shuffle(ansOptions))
      .catch((error) => console.log("error", error));
    
  }
  //set image and push pokemon name to array
  function setResults(results) {
    state.pokemon = results
    state.pokemon.sprite = state.pokemon.sprites.other["dream_world"].front_default 
    ansOptions.push(state.pokemon.name.toString())

    for (var i = 0; i < 4; i++) {
    fetch(`https://pokeapi.co/api/v2/pokemon/${rng()}`, requestOptions)
    .then((response) => {
      return response.json();
    })
    .then(answerOptions)
    .catch((error) => console.log("error", error));
    }
  }

  //random number from 1-150
  function rng() {
    return Math.floor((Math.random()*150)+1);
  }

  //get 3 random pokemon names and push them into array
  function answerOptions(results) {
    otherNames.pokemon = results;
    ansOptions.push(otherNames.pokemon.name.toString());
    randArray = shuffle(ansOptions);
  }

  //shuffle the names in the array
  function shuffle(arr) {
    var j, x, i;
    for (i = arr.length - 1; i > 0; i--) {
        j = Math.floor(Math.random() * (i + 1));
        x = arr[i];
        arr[i] = arr[j];
        arr[j] = x;
    }
    return arr;
  }

  //check if selected answer matches the pokemon from setResults function
  function selectAnswer(ans) {
    var clickedAnswer = ans;
    console.log("clicked on " + clickedAnswer);
    if (clickedAnswer === state.pokemon.name.toString()) {
      this.score += 1;
    } else {
      alert(`INCORRECT! That Pokémon is ${state.pokemon.name.charAt(0).toUpperCase()+state.pokemon.name.slice(1)}!`);
    }
    fetchPokemon();
  }

</script>

<template>
  <div id="quizApp">
    <div id="main">
      <h3>Current Score: {{ score }}</h3>
      <h1>Who is this Pokémon?</h1>
      <div id="image"><img :src="state.pokemon.sprite" /></div>
    </div>
    <div id="answers">
        <button class="button" v-for="name in randArray" @click="selectAnswer(name)"> {{ name.charAt(0).toUpperCase() + name.slice(1) }} </button>

    </div> <!--end div answers-->
  </div> <!--end div quizApp-->
</template>

<style>
@import "./assets/base.css";
body {
  background-color: #1d3557;
  align-items: center;
  margin-left: 200px;
  margin-right: 200px;

}
#quizApp {
  display: grid;
  grid-template-columns: 1fr 1fr;
  border: 2px solid white;
  max-width: 1400px;
  min-width: 800px;
  margin: 0 auto;
  margin-top: 25px;
  padding: 2rem;
  font-weight: normal;
  background-color: #457b9d;
  border-radius: 10px;
  box-shadow: 10px 10px 5px black;
}

#image {
  max-width: 700px;
  display: block;
  margin:0 auto;
  padding-bottom: auto;
  text-align: center;
}
.button {
  width: 400px;
  display: block;
  margin-bottom: 10px;
  font-size: 30px;
  padding: 25px 100px 25px;
  text-align: center;
  border-radius: 8px;
  transition: 0.3s;
  cursor: pointer;
  box-shadow: 5px 5px 2px black;
  font-family: 'montserrat','sans-serif';
  background-color: #83c5be;
}

.button:hover {
  background-color: #a8dadc;
  color: black;
  box-shadow: 0 12px 16px 0 rgba(0,0,0,0.24), 0 17px 50px 0 rgba(0,0,0,0.19);
}

h1, h3 {
  text-align: center;
  color: #f1faee;
}

#answers {
  
}

</style>
