<script setup>
import { onMounted } from 'vue';
onMounted(() => {
    //canvas
    const canvas = document.querySelector('#mycanvas');
    const ctx = canvas.getContext('2d');
    ctx.fillStyle = 'green';
    ctx.fillRect(10, 10, 100, 100);
    //webcamera
    const video = document.getElementById("video")
    navigator.mediaDevices.getUserMedia({
        video: true,
        audio: false,
    }).then(stream => {
        video.srcObject = stream;
        video.play()
    }).catch(e => {
        console.log(e)
    })
    // video要素の映像をcanvasに描画する
    function _canvasUpdate() {
        ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
        requestAnimationFrame(_canvasUpdate);
    };
    _canvasUpdate();
})
</script>

<style scoped>
.canvas {
    border: 1px solid #000;
}
</style>
<template>
    <div>
        <h1>Canvas</h1>
        <canvas width="200" height="200" class="border" id="mycanvas"></canvas>
    </div>
    <div>
        <h1>WEBカメラの映像を表示</h1>
        <div>
            <video id="video"></video>
        </div>
    </div>
</template>