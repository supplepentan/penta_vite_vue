<script setup>
import { onMounted } from 'vue';
onMounted(() => {
    function drawImage1() {
        const img1 = new Image();
        img1.src = "/src/assets/img/animal.png";
        img1.onload = () => {
            const canvas = document.querySelector("#image1");
            const ctx = canvas.getContext("2d");
            ctx.drawImage(img1, 0, 0, canvas.width, canvas.height);
        }
    }
    function drawImage2() {
        const img2 = new Image();
        img2.src = "/src/assets/img/supplepentan_t.png";
        img2.onload = () => {
            const canvas = document.querySelector("#image2");
            const ctx = canvas.getContext("2d");
            ctx.drawImage(img2, 0, 0, canvas.width, canvas.height);
        }
    };
    // #image1に画像を描画
    drawImage1();
    // #image2にテキストを描画
    drawImage2();

})
async function concatCanvas(image1, image2) {
    const canvas = document.querySelector(image1);
    const ctx = canvas.getContext("2d");
    for (let i = 0; i < image2.length; i++) {
        const imageMix = await getImagefromCanvas(image2[i]);
        ctx.drawImage(imageMix, 0, 0, canvas.width, canvas.height);
    }
};
function eraseCanvas(target) {
    const canvas = document.querySelector(target);
    const ctx = canvas.getContext("2d");
    ctx.clearRect(0, 0, canvas.width, canvas.height);
}
function getImagefromCanvas(id) {
    return new Promise((resolve, reject) => {
        const image = new Image();
        const ctx = document.querySelector(id).getContext("2d");
        image.onload = () => resolve(image);
        image.onerror = (e) => reject(e);
        image.src = ctx.canvas.toDataURL();
    });
};
// 「+」ボタンを押したら合成
const imgComp = () => {
    concatCanvas("#concat", ["#image1", "#image2"]);
};
// 「消しゴム」ボタンを押したらクリア
const imgClear = () => {
    eraseCanvas("#concat");
};
const getImage = () => {
    const canvas = document.querySelector("#concat");
    const link = document.createElement('a');
    link.href = canvas.toDataURL();
    link.download = 'export_image.png';
    link.click();

}
</script>
<template>
    <div>
        <h1>Canvas</h1>
        <div class="flex flex-wrap content-center h-48 bg-gray-200">
            <!-- 合成するcanvasその1 -->
            <div class="w-1/5 p-2">
                <canvas id="image1" class="border" width="200" height="170"></canvas>
            </div>
            <div class="w-1/5 p-2">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-plus-lg"
                    viewBox="0 0 16 16">
                    <path fill-rule="evenodd"
                        d="M8 2a.5.5 0 0 1 .5.5v5h5a.5.5 0 0 1 0 1h-5v5a.5.5 0 0 1-1 0v-5h-5a.5.5 0 0 1 0-1h5v-5A.5.5 0 0 1 8 2Z" />
                </svg>
            </div>
            <!-- 合成するcanvasその2 -->
            <div class="w-1/5 p-2">
                <canvas id="image2" class="border" width="200" height="170"></canvas>
            </div>
            <div class="w-1/5 p-2">
                <p><button type="button" id="btn-concat" @click="imgComp">
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
                            class="bi bi-caret-right" viewBox="0 0 16 16">
                            <path
                                d="M6 12.796V3.204L11.481 8 6 12.796zm.659.753 5.48-4.796a1 1 0 0 0 0-1.506L6.66 2.451C6.011 1.885 5 2.345 5 3.204v9.592a1 1 0 0 0 1.659.753z" />
                        </svg>
                    </button></p>
            </div>
            <!-- 合成結果用のcanvas -->
            <div class="w-1/5 p-2">
                <canvas id="concat" class="border" width="200" height="170"></canvas><!-- 消しゴム -->
            </div>
            <div class="w-1/2 p-2">
                <p id="eraser">
                    <button type="button" id="btn-eraser" @click="imgClear">
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
                            class="bi bi-slack" viewBox="0 0 16 16">
                            <path
                                d="M3.362 10.11c0 .926-.756 1.681-1.681 1.681S0 11.036 0 10.111C0 9.186.756 8.43 1.68 8.43h1.682v1.68zm.846 0c0-.924.756-1.68 1.681-1.68s1.681.756 1.681 1.68v4.21c0 .924-.756 1.68-1.68 1.68a1.685 1.685 0 0 1-1.682-1.68v-4.21zM5.89 3.362c-.926 0-1.682-.756-1.682-1.681S4.964 0 5.89 0s1.68.756 1.68 1.68v1.682H5.89zm0 .846c.924 0 1.68.756 1.68 1.681S6.814 7.57 5.89 7.57H1.68C.757 7.57 0 6.814 0 5.89c0-.926.756-1.682 1.68-1.682h4.21zm6.749 1.682c0-.926.755-1.682 1.68-1.682.925 0 1.681.756 1.681 1.681s-.756 1.681-1.68 1.681h-1.681V5.89zm-.848 0c0 .924-.755 1.68-1.68 1.68A1.685 1.685 0 0 1 8.43 5.89V1.68C8.43.757 9.186 0 10.11 0c.926 0 1.681.756 1.681 1.68v4.21zm-1.681 6.748c.926 0 1.682.756 1.682 1.681S11.036 16 10.11 16s-1.681-.756-1.681-1.68v-1.682h1.68zm0-.847c-.924 0-1.68-.755-1.68-1.68 0-.925.756-1.681 1.68-1.681h4.21c.924 0 1.68.756 1.68 1.68 0 .926-.756 1.681-1.68 1.681h-4.21z" />
                        </svg>
                    </button>
                </p>
            </div>
            <div class="w-1/2 p-2">
                <p id="eraser">
                    <button type="button" id="btn-eraser" @click="getImage">
                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
                            class="bi bi-cloud-download" viewBox="0 0 16 16">
                            <path
                                d="M4.406 1.342A5.53 5.53 0 0 1 8 0c2.69 0 4.923 2 5.166 4.579C14.758 4.804 16 6.137 16 7.773 16 9.569 14.502 11 12.687 11H10a.5.5 0 0 1 0-1h2.688C13.979 10 15 8.988 15 7.773c0-1.216-1.02-2.228-2.313-2.228h-.5v-.5C12.188 2.825 10.328 1 8 1a4.53 4.53 0 0 0-2.941 1.1c-.757.652-1.153 1.438-1.153 2.055v.448l-.445.049C2.064 4.805 1 5.952 1 7.318 1 8.785 2.23 10 3.781 10H6a.5.5 0 0 1 0 1H3.781C1.708 11 0 9.366 0 7.318c0-1.763 1.266-3.223 2.942-3.593.143-.863.698-1.723 1.464-2.383z" />
                            <path
                                d="M7.646 15.854a.5.5 0 0 0 .708 0l3-3a.5.5 0 0 0-.708-.708L8.5 14.293V5.5a.5.5 0 0 0-1 0v8.793l-2.146-2.147a.5.5 0 0 0-.708.708l3 3z" />
                        </svg>
                    </button>
                </p>
            </div>
        </div>
    </div>
</template>

<style scoped>
.canvas {
    border: 1px solid #000;
}
</style>
