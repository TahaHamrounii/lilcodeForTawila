<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();
const chosenUSer = ref({});

onMounted(()=> {
  const sentId = route.query.SentId;
  fetch(`http://localhost:3000/scoreboard/${sentId}`)
  .then(response => response.json())
  .then(data => {
    chosenUSer.value = data;
  });
})


</script>

<template>
<div>
  <h1>Details</h1>
  <p>{{chosenUSer.task}}</p>
  <p>{{chosenUSer.checked}}</p>
  <p>{{chosenUSer.showit}}</p>
</div>
</template>

