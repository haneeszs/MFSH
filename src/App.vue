<script setup>
import { ref, onUnmounted } from 'vue'
import PocketBase from 'pocketbase';

const pb = new PocketBase('https://127.0.0.1:8090')
const hi = "CITIES"
const isTrue = ref(true)
const solatAppLink = "https://www.e-solat.gov.my/index.php?r=esolatApi/takwimsolat&period=today&zone=SGR01"
const name = ref("")
const number = ref(0)
const prayerTime = ref({})
const capitalCities = ref(["Jakarta", "Paris", "Kuala Lumpur", "Rome"])


function increment() {
  number.value++
}

function startInterval() {
  if (!intervalId) {
    intervalId = setInterval(increment, 1000)
  }
}

// Clear interval when component unmounts
onUnmounted(() => {
  if (intervalId) clearInterval(intervalId)
})

function addCity() {
  const trimmedName = name.value.trim()
  if (trimmedName) {
    capitalCities.value.push(capitalizeFirstLetter(trimmedName))
    name.value = ""
  }
}

function capitalizeFirstLetter(str) {
  return str.charAt(0).toUpperCase() + str.slice(1)
}

async function getPrayerTime() {
  try {
    const response = await fetch(solatAppLink)
    const data = await response.json()
    prayerTime.value = data.prayerTime[0] || {}
    console.log(prayerTime.value)
  } catch (error) {
    console.error("Error fetching prayer time:", error)
  }
}

function createTo_do()
{
  const newTodo = {
    item: to_doInput.value,
    isDone : false
  }
  pb.collection('to_do').create(newTodo).then(res =>{
  RecordService.value.push(res)
    to_doInput.value = " "
  }
  
)
  }
</script>

<template>
  <section>
    <h1>{{ number }}</h1> 
    <button @click="startInterval">Start Increment</button>

    <div>
      <input type="text" v-model="name" placeholder="Enter a city" />
      <button @click="addCity">Add City</button>
    </div>

    <h1>{{ hi }}</h1> 

    <ol>
      <li v-for="(city, index) in capitalCities" :key="index">{{ city }}</li>
    </ol>

    <h1 v-if="isTrue">Melakao is my city</h1>
    <button @click="isTrue = !isTrue">{{ isTrue ? "Hide" : "Show" }}</button>

    <button @click="getPrayerTime">Get Prayer Time</button>

    <!-- Prayer Times Table -->
    <div v-if="Object.keys(prayerTime).length" class="prayer-table">
      <h2>Prayer Times for Today</h2>
      <table>
        <thead>
          <tr>
            <th>Prayer</th>
            <th>Time</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(time, prayer) in prayerTime" :key="prayer">
            <td>{{ prayer }}</td>
            <td>{{ time }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </section>
</template>

<style scoped>
section {
  padding: 1em;
  max-width: 600px;
  margin: auto;
  font-family: Arial, sans-serif;
}

input {
  padding: 0.5em;
  margin-right: 0.5em;
  width: 60%;
}

button {
  margin-top: 0.5em;
  padding: 0.5em 1em;
  cursor: pointer;
  background-color: #e3b9dc;
  color: rgb(0, 0, 0);
  border: none;
  border-radius: 5px;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #e1a0e1;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 1em;
}

th, td {
  padding: 0.75em;
  text-align: left;
  border-bottom: 1px solid #dfa7d6;
}

th {
  background-color: #ddb5e3;
}

h1, h2 {
  margin-top: 1em;
}
</style>