<script setup>
//https://blog.mahoroi.com/posts/2020/05/browser-head-pose-estimation/
import * as faceapi from 'face-api.js';
import { onMounted } from 'vue';
var video;
var canvas;
const webCameraOn = () => {
    navigator.mediaDevices.getUserMedia({
        video: true,
        audio: false,
    }).then(stream => {
        video.srcObject = stream;
        video.play()
        detect();
    }).catch(e => {
        console.log(e)
    })
};
async function detect() {
    requestAnimationFrame(detect);

    //  webカメラの映像から顔認識を行う
    const useTinyModel = true;
    const detection = await faceapi
        .detectSingleFace(
            video,
            new faceapi.TinyFaceDetectorOptions({
                inputSize: 160,
            })
        )
        .withFaceLandmarks(useTinyModel);

    if (!detection) {
        return;
    }

    // 認識データをリサイズ
    const resizedDetection = faceapi.resizeResults(detection, {
        width: video.width,
        height: video.height,
    });

    // ランドマークをキャンバスに描画
    canvas = document.getElementById('canvas1');
    canvas.getContext('2d').clearRect(0, 0, canvas.width, canvas.height);
    faceapi.draw.drawFaceLandmarks(canvas, resizedDetection);
    // 以後使用するランドマーク座標
    const landmarks = resizedDetection.landmarks;
    const nose = landmarks.getNose()[3];
    const leftEye = landmarks.getLeftEye()[0];
    const rightEye = landmarks.getRightEye()[3];
    const jaw = landmarks.getJawOutline()[8];
    const leftMouth = landmarks.getMouth()[0];
    const rightMouth = landmarks.getMouth()[6];
    const leftOutline = landmarks.getJawOutline()[0];
    const rightOutline = landmarks.getJawOutline()[16];
}
onMounted(() => {
    video = document.getElementById("video")
    Promise.all([
        faceapi.nets.tinyFaceDetector.loadFromUri('/models/weights'),
        faceapi.nets.faceLandmark68TinyNet.loadFromUri('/models/weights'),
    ]).then(webCameraOn);
})

</script>
<template>
    <div>
        <h1>WEBカメラの映像を表示</h1>
        <div style="position: relative;">
            <video id="video" width="640" height="480" style="transform: scaleX(-1);"></video>
            <canvas id="canvas1" width="640" height="480"
                style="position: absolute; top: 0px; left: 0px; transform: scaleX(-1);"></canvas>
            <canvas id="canvas2" width="640" height="480"
                style="position: absolute; top: 0px; left: 0px; transform: scaleX(-1);"></canvas>
        </div>
    </div>
</template>