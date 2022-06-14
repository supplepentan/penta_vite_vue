<script setup>
import { ref } from 'vue';
import WebCamera from './parts/WebCamera.vue';

const count = ref(0)
</script>

<template>
    <h1>メイン</h1>
    <WebCamera />
</template>

<style scoped>
</style>
