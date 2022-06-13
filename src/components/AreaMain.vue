<script setup>
import { ref } from 'vue';
import Capture from './parts/Capture.vue';
import RabbitCount from './parts/RabbitCount.vue';

const count = ref(0)
</script>

<template>
    <h1>メイン</h1>
    <RabbitCount />
    <Capture />
</template>

<style scoped>
</style>
