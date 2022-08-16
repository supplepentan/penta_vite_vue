<script setup>
import { ref } from "vue";
const my_text = ref("");
const copyToClipboard = (text) => {
  navigator.clipboard.writeText(text)
    .then(() => {
      console.log(text);
      console.log("copied!");
    })
    .catch(e => {
      console.error(e)
    })
}
</script>
<template>
  <div>
    <h1 class="text-center">Clipboard</h1>
    <div class="w-full px-2">
      <textarea v-model="my_text" placeholder="ここに入力" class="w-full h-48 px-2 border-2"></textarea>
      <button type="button" @click="copyToClipboard(my_text)"
        class="block p-2 bg-gray-200 border rounded-full">COPY!</button>
    </div>
  </div>
</template>