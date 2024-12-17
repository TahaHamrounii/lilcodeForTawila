<script setup>
import { onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'
const listOfTasks = ref([]);
const newtask = ref('')
const checked = ref(false)
const showit = ref(false)
const newname = ref('')
const idCounter = ref(0);
const router = useRouter();

function getAllUsers(){
  fetch(`http://localhost:3000/scoreboard`)
  .then(response => response.json())
  .then(data => {
    listOfTasks.value = data;
    idCounter.value = data.length;
  });
}

onMounted(()=>{
  getAllUsers()
})


function addTask(){
  if(newtask.value.trim() !== ''){
    idCounter.value++;
    fetch(`http://localhost:3000/scoreboard`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      id: idCounter.value.toString(),
      task: newtask.value,
      checked:checked.value,
      showit:showit.value
    })
}).then(getAllUsers)


    newtask.value='';
    checked.value = false;
    showit.value = false;
  }
}

function checkit(id){
  const task = listOfTasks.value.find((task) => task.id === id);

  fetch(`http://localhost:3000/scoreboard/${id}`, {
    method: 'PATCH',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      id: task.id.toString(),
      task: task.task,
      checked: !task.checked,
      showit: task.showit
    })
  }).then(getAllUsers)



}

function showmore(id){
  const task = listOfTasks.value.find((task) => task.id === id);
  if (task) {
    if(task.showit == false){
      task.showit = true;
    }
    else{task.showit = false}
  }
  console.log(listOfTasks.value)
}

function change(id){
  const task = listOfTasks.value.find((task) => task.id === id);

  fetch(`http://localhost:3000/scoreboard/${id}`, {
   method: 'PUT',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      id: task.id.toString(),
      task: newname.value,
      checked: task.checked,
      showit: task.showit
    })
  }).then(getAllUsers)

}

function Delete(id){
  fetch(`http://localhost:3000/scoreboard/${id}`, {
    method: 'DELETE',
    headers: {
      'Content-Type': 'application/json'
    }
  }).then(getAllUsers)

}

function Teleport(myId){
  console.log("sent ID" + myId)
  router.push({ name: "leaderboard", query: { SentId: myId } });
}
console.log(listOfTasks)
</script>

<template>
  <input type="text" v-model="newtask">
  <button @click="addTask">add</button>
  <label>
    checked
    <input type="checkbox" v-model="checked">
  </label>
  <br><br>
  <ul>
    <li v-for="(task) in listOfTasks" :key="task.id">
      <div v-if="task.checked==true" style="text-decoration: line-through;">
        {{ task.task }}
      </div>
      <div v-else>
        {{task.task}} <button @click="checkit(task.id)">✔</button>
      </div>
      <button @click="showmore(task.id)">
        {{ task.showit ? "Hide" : "Show More" }}
      </button>
      <div v-if="task.showit">
        <div>
          <button @click="Teleport(task.id)">show in other page</button>
        </div>
        <div>
          <label>Modify : <input type="text" placeholder="new name" v-model="newname"></label>
          <button @click="change(task.id)">change</button>
        </div>
        <div>
          Delete task <button @click="Delete(task.id)">Delete</button>
        </div>
      </div>
    </li>
  </ul>
</template>