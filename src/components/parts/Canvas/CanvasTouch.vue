<script setup>
import { onMounted, ref } from 'vue';
const canvas = ref();
const ctx = ref();
onMounted(() => {
    canvas.value = document.querySelector('#canvas');
    ctx.value = canvas.value.getContext('2d');
    canvas.value.addEventListener('mousemove', mouseMove);
    function mouseMove(event) {
        ctx.value.beginPath();
        ctx.value.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI);
        ctx.value.stroke();
    }
});
function cls() {
    //キャンバスの(0,0)～(200,200)の範囲をクリアする
    console.log("イレイザー");
    ctx.value.clearRect(0, 0, canvas.value.width, canvas.value.height);
};
const getImage = () => {
    console.log("ゲットイメージ");
    const link = document.createElement('a');
    link.href = canvas.value.toDataURL();
    link.download = 'export_image.png';
    link.click();
}
</script>
<template>
    <div>
        <h1>Canvas</h1>
        <button type="button" class="px-4 py-2 font-bold text-white bg-blue-500 rounded hover:bg-blue-700"
            v-on:click="cls">Clear</button>
        <button type="button" class="px-4 py-2 font-bold text-white bg-blue-500 rounded hover:bg-blue-700"
            v-on:click="getImage">Capture</button>
        <canvas id="canvas" width="400" height="250"
            style="border:1px solid #000000; background: #ffffe8; max-width: 100%; height: auto; max-height: 100%">
            このブラウザはHTML5のCanvas要素に対応していません。
        </canvas>
    </div>
</template>

<style scoped>
#canvas {
    border: 1px solid #000;
}
</style>