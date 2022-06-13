<script setup>
import { ref } from 'vue';
const words = ref("🐰")
const addRabbit = () => {
    console.log("🐰")
    words.value += "🐰"
}
const calearRabbit = () => {
    words.value = ""
}
</script>

<template>
    <button @click="addRabbit">🐰+</button>
    <button @click="calearRabbit">🐰-</button>
    <div>{{ words }}</div>
</template>