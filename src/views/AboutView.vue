<script setup>
import { ref, onMounted } from 'vue';
const players = ref([]);

function loadAllPlayers(){
  fetch('http://localhost:3000/scoreboard')
  .then(response => response.json())
  .then(data => 
    players.value = data
  )
  }


function deletefromTable(id){
  fetch(`http://localhost:3000/scoreboard/${id}`, {
    method: 'DELETE',
    headers: {
      'Content-Type': 'application/json'
    }
  }).then(
    loadAllPlayers
  )
}

onMounted(()=>{
  loadAllPlayers()
})
</script>

<template>

<div v-for="player in players" :key="player.id">
  <p>{{player.name}}: {{player.score}}</p>
  <button @click="deletefromTable(player.id)">delete</button>
</div>

</template>
<scrip ></scrip>

