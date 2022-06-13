<script setup>
import html2canvas from "html2canvas";
const captureImage = () => {
    html2canvas(document.querySelector('#capture')).then((canvas) => {
        const link = document.createElement('a')
        link.href = canvas.toDataURL()
        link.download = `export_image.png`
        link.click()
    })
}
</script>
<template>
    <v-btn id="save-btn" @click="captureImage">
        画像を保存
    </v-btn>
    <div id="capture">
        <h1>きゃぷちゃ</h1>
    </div>
</template>