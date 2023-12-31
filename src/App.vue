<script setup>

// Imports ref
import { onMounted, ref } from 'vue'

import Header from './components/header/header.vue'
import Devices from './components/devices/devices.vue'
import Commands from './components/commands/commands.vue'
import Sequences from './components/sequences/sequences.vue'

// Imports protocol
import { Protocol, MessageType, CommandType } from './scripts/protocol/protocol.js'

var protocol = null

const allSequences = ref([])
const allDevices = ref([])

const currentDevice = ref('')
const isRecording = ref(false)
const currentSequence = ref([])
const currentSequenceName = ref('')

function buildProtocolPayload(buttonName) {
  /* Select the command */
  var command = null

  switch (buttonName) {
    case 'release':
      command = CommandType.Release
      break

    case 'up':
      command = CommandType.Up
      break

    case 'press':
      command = CommandType.Grab
      break

    case 'rotate_ccw':
      command = CommandType.RotateCcw
      break

    case 'forward':
      command = CommandType.Forward
      break

    case 'rotate_cw':
      command = CommandType.RotateCw
      break

    case 'left':
      command = CommandType.Left
      break

    case 'backward':
      command = CommandType.Backward
      break

    case 'right':
      command = CommandType.Right
      break

    case 'down':
      command = CommandType.Down
      break

    // FIXME add the other commands (set-zero and go-to-zero and stop) and messages
  }

  /* Build the payload object */
  return { command: command, duration: 2 }
}

const handleClickParent = async (buttonName, image) => {
  console.log('button-clicked-parent from the parent component', image);
  if (isRecording.value) {
    currentSequence.value.push(image)
  }
  else {
    if (currentDevice.value == null || currentDevice.value == "") {
      alert("No device selected.")
      return
    }

    // Build a payload matching the buttonName command
    const payload = buildProtocolPayload(buttonName)

    console.log(payload);

    const encodedPayload = protocol.encode(CommandType.TimedCommand, payload)

    // Sends the requests and wait for the response
    const response = await fetch(currentDevice.ip, {
      method: "POST",
      body: encodedPayload,
    })

    // Gets the response body
    const encodedText = await response.text()

    // Decode the response
    const responseObj = protocol.decode(MessageType.CommandResponse,
      encodedText)
  }
}

const handleNewSequence = () => {
  isRecording.value = !isRecording.value
  console.log('Current recording state', isRecording.value);
  if (isRecording.value) {
    currentSequence.value = []
    currentSequenceName.value = prompt("Please enter the sequence name", "New Sequence");
    if (currentSequenceName.value == null || currentSequenceName.value == "") {
      isRecording.value = false
      alert('Sequence name cannot be empty')
      return
    }
    if (allSequences.value.find(sequence => sequence.name == currentSequenceName.value)) {
      isRecording.value = false
      alert('Sequence name already exists')

      return
    }

  } else {
    if (currentSequence.value.length == 0) {
      alert('Sequence cannot be empty')
      return
    }
    console.log('final sequence', currentSequence.value);
    allSequences.value.push({
      name: currentSequenceName.value,
      movements: currentSequence.value
    })
  }
}

const handleNewDevice = () => {
  const deviceName = prompt("Please enter the device name", "New Device");
  const deviceIp = prompt("Please enter the device ip", "192.168.1.0");
  if (deviceName == null || deviceName == "") {
    alert('Device name cannot be empty')
    return
  }

  if (deviceIp == null || deviceIp == "") {
    alert('Device ip cannot be empty')
    return
  }

  if (allDevices.value.find(device => device.name == deviceName)) {
    alert('Device name already exists')
    return
  }

  if (allDevices.value.find(device => device.ip == deviceIp)) {
    alert('Device ip already exists')
    return
  }

  let newDevice = {
    name: deviceName,
    ip: deviceIp,
    active: allDevices.value.length == 0,
    id: Date.now()
  }

  if (allDevices.value.length == 0) {
    currentDevice.value = newDevice
  }

  allDevices.value.push(newDevice)

  console.log(allDevices.value)
}

const handleDeviceClicked = (deviceId) => {
  for (var i = 0; i < allDevices.value.length; i++) {
    if (allDevices.value[i].id == deviceId) {
      currentDevice.value = allDevices.value[i]
      break
    }
  }
}

const handleDeleteSequence = (sequenceName) => {
  allSequences.value = allSequences.value.filter(sequence => sequence.name != sequenceName)
}

const handlePlaySequence = async (sequenceName) => {
  const sequence = allSequences.value.find(sequence => sequence.name == sequenceName)
  console.log('playing sequence', sequence);
}

const handleDeleteDevice = () => {
  const devicename = prompt("Please enter the device name", "New Device");
  if (devicename == null || devicename == "") {
    alert('Device name cannot be empty')
    return
  }

  const found = allDevices.value.find(device => device.name == devicename)
  if (!found) {
    alert('Device not found')
  }
  for (var i = 0; i < allDevices.value.length; i++) {
    if (allDevices.value[i].name == devicename) {
      allDevices.value.splice(i, 1);
      break;
    }
  }

  if (allDevices.value.length > 0) {
    currentDevice.value = allDevices.value[0]
  }
  else {
    currentDevice.value = ''
  }
}


onMounted(async () => {
  protocol = await Protocol.load("armwar.proto")
})

</script>

<template>
  <div class="container">

    <div class="main-container">
      <Header :name="currentDevice.name" />
      <div class="content">
        <Devices v-bind:allDevices="allDevices" @new-device="handleNewDevice"
          @device-clicked="handleDeviceClicked"
          @delete-device="handleDeleteDevice" />
        <Commands @button-clicked-parent="handleClickParent" />
        <Sequences @new-sequence="handleNewSequence"
          v-bind:isRecording="isRecording" v-bind:allSequences="allSequences"
          @delete-sequence="handleDeleteSequence"
          @play-sequence="handlePlaySequence" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  background-color: var(--background);
  height: 100vh;
  width: 100vw;
  overflow-x: hidden;
}

.main-container {
  max-width: 1600px;
  margin: 0 auto;
}

.content {
  background-color: var(--content);
  border-radius: 20px 20px 0 0;
  min-height: calc(100vh - 70px);
  display: grid;
  grid-template-columns: 30% 30% 30%;
  gap: 5%;

}

:global(body) {
  margin: 0;
  padding: 0;
}

* {
  --background: #131d35;

  --green: #aff8af;

  --content: #242d44;

  --primary: #2e364a;
  --secondary: #53607c;

  --terciary: #404b62;
  --terciary-hover: #2e364a;

  --white-text: #f0f0f0;

  font-family: 'AR One Sans', sans-serif;
}
</style>
