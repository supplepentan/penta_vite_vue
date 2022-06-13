<script setup>
import { ref } from 'vue';
import TextCount from './parts/TextCount.vue';

const count = ref(0)
</script>

<template>
    <h1>メイン</h1>
    <TextCount />
</template>

<style scoped>
</style>
