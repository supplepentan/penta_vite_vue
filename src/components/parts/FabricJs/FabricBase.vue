<script setup>
//https://qiita.com/kurukuruz/items/04866272867f4ace3a17
//https://qiita.com/kurukuruz/items/966f0c6942df73f35f46
//https://erabikata.info/javascript-canvas-fabric-image.html
import { fabric } from "fabric";
import { onMounted } from "vue";

var canvas;
// create a rectangle object
const rect = new fabric.Rect({
  left: 200,
  top: 100,
  fill: 'red',
  width: 31,
  height: 31,
});

onMounted(() => {
  /*
  // create a wrapper around native canvas element
  const canvas = new fabric.Canvas("myCanvas");

  // "add" rectangle onto canvas
  canvas.add(rect);
  //image
  fabric.Image.fromURL("/supplepentan-favicon.png", function (oImg) {
    oImg.scale(2);
    canvas.add(oImg);
  });
  */
});
//ファイルのロード
const loadFile = async (e) => {
  const img = new Image();
  img.onload = function () {
    canvas = new fabric.Canvas("myCanvas");
    //image
    fabric.Image.fromURL(img.src, function (oImg) {
      oImg.scale(1);
      canvas.add(oImg);
      oImg.hasBorders = false;//枠線をなくす
      oImg.hasControls = false;//枠線の□をなくす
    });
  };
  img.src = URL.createObjectURL(e.target.files[0]);
};
//canvasイメージをダウンロード
const getImage = () => {
  const imageCanvas = document.querySelector("#myCanvas");
  const link = document.createElement('a');
  link.href = imageCanvas.toDataURL();
  link.download = 'export_image.png';
  link.click();
};
// 特定要素の削除
const elmDelete = () => {
  //選択されているオブジェクトを削除する。
  let activeObjects = canvas.getActiveObjects();

  if (activeObjects != null) {
    if (confirm('選択された箇所を全て削除しますか？')) {
      activeObjects.forEach(obj => {

        //対象オブジェクトを削除
        canvas.remove(obj);

        //矩形のIDとcanvas.item紐づけ用配列も削除する。
        let arrIndex = arrayRect.indexOf(obj.id);
        arrayRect.splice(arrIndex, 1);
      });
    }
  } else {
    alert("オブジェクトが選択されていません。");
  }
};
</script>
<template>
  <div class="m-2">
    <h1 class="flex-none text-center">Fabric JS</h1>
    <div class="flex justify-center w-full m-2">
      <canvas id="myCanvas" width=500 height=500 class="block border-2 bg-gray400"></canvas>
    </div>
    <div>
      <div class="flex justify-center">
        <label for="loadFile" class="">Uploading Image</label>
        <input type="file" id="loadFile" v-on:change="loadFile" required minlength="4" maxlength="8" size="10">
      </div>
      <div class="flex justify-center">
        <button type="button" v-on:click="getImage" class="p-2 border-2">Capture</button>
        <button type="button" v-on:click="elmDelete" class="p-2 border-2">Delete</button>
      </div>
    </div>
  </div>
</template>