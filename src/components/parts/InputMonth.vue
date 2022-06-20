<script setup>
import { ref } from 'vue';
const date = ref();
</script>
<template>
    <div>
        <input type="month" id="month" v-model="date">
        <p>日付：{{ date }}</p>
    </div>
</template>