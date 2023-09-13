<script setup>

// Imports ref
import { ref } from 'vue'

// Imports protobuf
import { load } from "protobufjs"

// Variables
var uint8Encoder = new TextEncoder();
var protobuf = null
var demo = null
var inputName = null
var inputAge = null
var displayedName = ref(null)
var displayedAge = ref(null)

// Async function that initializes protobuf with .proto file
async function loadProtobuf() {
  protobuf = await load("armwar.proto")
  demo = protobuf.lookupType("armwar.demo")
}

// Function that sends a post request to the Controller and retrieves its response
async function sendMessage() {
  if (protobuf == null || demo == null)
    alert("Protobuf is still loading, wait a few seconds")

  // Builds the payload thanks to the user data 
  const payload = demo.create({ name: inputName, age: parseInt(inputAge) })

  // Encode the payload to create the message
  const message = demo.encode(payload).finish();

  // Sends the requests
  const response = await fetch("http://192.168.1.65/", {
    method: "POST",
    body: message,
  })

  // Retrieves the body response as a String
  const encodedText = await response.text()

  // Encode the response in uint8 and decode the object 
  const responseObj = demo.decode(uint8Encoder.encode(encodedText))

  // Update the values
  displayedName.value = responseObj.name
  displayedAge.value = responseObj.age
}

// Call asynchronously protobuf loading
loadProtobuf();
</script>

<template>
  <div id="container">
    <button @click="sendMessage">SEND</button>
    <input type="text" v-model="inputName" placeholder="Name" />
    <input type="text" v-model="inputAge" placeholder="Age" />
    <h3>Received back</h3>
    <div id="answers">
      <p class="circle">{{ displayedName == null ? " " : displayedName }}</p>
      <p class="circle">{{ displayedAge == null ? " " : displayedAge }}</p>
    </div>
  </div>
</template>

<style scoped>
#container {
  display: flex;
  flex-direction: column;
}

button {
  display: block;
  width: 200px;
  height: 50px;
  border-radius: 5px;
  border: none;
  background-color: #33ceff;
  margin: auto;
  cursor: pointer;
}

button:hover {
  background-color: #33f5ff;
  transition: 0.2s;
}

input {
  display: block;
  width: 200px;
  margin: auto;
  margin-top: 25px;
  border-style: none;
  background-color: #1f2222;
  color: white;
  padding: 10px;
}

input:focus {
  outline: none;
}

#answers {
  width: 400px;
  margin: auto;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
}

h3 {
  margin-top: 50px;
  font-weight: bold;
  text-align: center;
}

.circle {
  text-align: center;
  line-height: 100px;
  display: inline-block;
  width: 100px;
  height: 100px;
  border: 2px solid #FFC300;
  border-radius: 100px;
}
</style>
