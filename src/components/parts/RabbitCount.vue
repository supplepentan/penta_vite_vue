<script setup>
import { ref } from 'vue';
const words = ref("🐰")
const rabbitIncrease = () => {
    words.value += "🐰"
}
const rabbitDecrease = () => {
    words.value -= "🐰"
}
const rabbitCrear = () => {
    words.value = ""
}
</script>

<template>
    <div>
        <button @click="rabbitIncrease">🐰+</button>
        <button @click="rabbitCrear">🐰!</button>
        <button @click="rabbitDecrease">🐰-</button>
        <div>{{ words }}</div>
    </div>
</template>

<style scoped>
</style>