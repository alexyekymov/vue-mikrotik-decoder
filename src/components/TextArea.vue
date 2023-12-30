<script setup>
import { computed, ref } from 'vue'

const message = ref("")

const buttonDisabled = computed(() => message.value.length === 0)

class Message {
  encodedMessage;

  constructor(encodedMessage) {
    this.encodedMessage = encodedMessage
  }
}

const onChangeTextArea = (event) => {
  message.value = event.target.value
  buttonPointerChange()
}

const buttonPointerChange = () => {
  const button = document.querySelector('button')
  if (buttonDisabled.value) {
    button.classList.remove('cursor-pointer')
    button.classList.add('cursor-not-allowed')
  } else {
    button.classList.remove('cursor-not-allowed')
    button.classList.add('cursor-pointer')
  }
}

const decodedMessage = ref("")

const sendMessageToDecode = () => {
  const dataMessage = new Message(message.value)
  fetch("https://overlax.pp.ua/decoder/api/v1/decode", {
    headers: {
      'Content-Type': 'application/json',
    },
    method: 'POST',
    body: JSON.stringify(dataMessage)
  })
    .then(res => res.json())
    .then(data => {
      decodedMessage.value = data.decodedMessage
    })
    .catch(error => {
      console.log(error)
    });
}

</script>

<template>
  <div class="w-3/6 grid m-auto gap-5 mt-5 mb-5">
    <textarea class="p-2"
              @input="onChangeTextArea"
              name="encoded" id="encoded" cols="100" rows="10" maxlength="600"
              placeholder="Max 600 symbols"></textarea>
    <button
      :disabled="buttonDisabled"
      @click="sendMessageToDecode"
      class="transition bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 cursor-not-allowed">
      Decode
    </button>

    <textarea class="p-2"
              v-text="decodedMessage"
              name="decoded" id="decoded" cols="100" rows="10" maxlength="600" readonly
              placeholder="Decoded message will be here..."></textarea>
  </div>
</template>

<style scoped>

</style>