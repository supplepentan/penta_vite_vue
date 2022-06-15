<template>
    <div>
        <h1>Canvas</h1>
        <canvas width="200" height="200" class="border" id="mycanvas"></canvas>
    </div>
</template>

<script setup>
import { onMounted } from 'vue';
onMounted(() => {
    const canvas = document.querySelector('#mycanvas');
    const ctx = canvas.getContext('2d');
    ctx.fillStyle = 'green';
    ctx.fillRect(10, 10, 100, 100);
})
</script>

<style scoped>
.canvas {
    border: 1px solid #000;
}
</style>