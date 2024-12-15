<script setup>
import { onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'
const guess = ref(0)
const listOfGuesses = ref([]);
const maxGuesses = ref(5)
const buttonIsShowing = ref(true)
const randomNumber = ref()
const score = ref(5)
const inputIsShowing = ref(false)
const nom = ref('');
const router = useRouter();

onMounted(()=>{
  randomNumber.value = Math.floor(Math.random() * 100)
})

function addGuess(){
  listOfGuesses.value.push(guess.value) 
  if(listOfGuesses.value.length >= maxGuesses.value){
    buttonIsShowing.value = false
  }
}

function treatAddition(){
  addGuess();
  if(guess.value != randomNumber.value){
    score.value --;
  }
  else{
    buttonIsShowing.value = false
  }
  guess.value = 0

}

function Rejouer() {
  listOfGuesses.value = []
  buttonIsShowing.value = true
  randomNumber.value = Math.floor(Math.random() * 100)
}

function showInput(){
  inputIsShowing.value = true
}
function soumettre(){
  fetch( 
  "http://localhost:3000/scoreboard", {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(
      {
      name: nom.value,
      score: score.value
      })
  }).then(

    Rejouer,
    inputIsShowing.value = false,
    router.push('/leaderboard')

  )
}
</script>

<template>
  {{ randomNumber }}

<input v-model="guess"> <button v-if="buttonIsShowing" @click="treatAddition">Take a guess</button>
<div v-for="guess in listOfGuesses">
 <div v-if="guess != randomNumber"> votre choix: {{ guess }} est plus <span v-if="guess<randomNumber"> petit</span>  <span v-else> grand</span></div>
  <div v-else >Bravo vous avez trouvé le bon nombre, votre score est: {{ score }}</div>

</div>
<div v-if="!buttonIsShowing">
  <button  @click="Rejouer"> Rejouer</button>
  <button  @click="showInput"> inserer vos données </button>
</div>

<div v-if="inputIsShowing">
  <input v-model="nom">
  <button @click="soumettre">submit</button>
</div>


</template>
