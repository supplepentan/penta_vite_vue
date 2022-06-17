<script setup>
import { onMounted } from 'vue';
onMounted(() => {
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
})
</script>
<template>
    <div>
        <h1>WEBカメラの映像を表示</h1>
        <div>
            <video id="video"></video>
        </div>
    </div>
</template>