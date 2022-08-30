<script setup>
//https://qiita.com/kurukuruz/items/04866272867f4ace3a17
//https://qiita.com/kurukuruz/items/966f0c6942df73f35f46
//https://erabikata.info/javascript-canvas-fabric-image.html
//https://zenn.dev/megeton/articles/aaad434c8533b6
import { fabric } from "fabric";
import { onMounted } from "vue";

var canvas;
var canvasFab;
// create a rectangle object
const rect = new fabric.Rect({
  left: 200,
  top: 100,
  fill: 'red',
  width: 31,
  height: 31,
});

onMounted(() => {
  canvas = document.getElementById("myCanvas");
  canvasFab = new fabric.Canvas("myCanvas", { preserveObjectStacking: true });
});
//ファイルのロード
const loadFile = async (e) => {
  const img = new Image();
  img.onload = function () {
    //☆canvsサイズを画像に合わせる
    //canvasFav.setDimensions({ width: img.width, height: img.height })
    //canvasFav.renderAll()
    //☆画像描画
    fabric.Image.fromURL(img.src, function (oImg) {
      //☆画像をcanvasサイズに合わせる
      if (img.width > img.height) {
        oImg.scaleToWidth(canvasFab.width)
      } else {
        oImg.scaleToHeight(canvasFab.height)
      };
      oImg.left = (canvasFab.width / 2);
      oImg.top = (canvasFab.height / 2);
      oImg.originX = "center";
      oImg.originY = "center";
      //☆画像描画の倍率
      //oImg.scale(1);
      canvasFab.add(oImg)
      //☆枠線をなくす
      oImg.hasBorders = true;
      //☆枠線の□をなくす
      oImg.hasControls = true;
    });
  };
  img.src = URL.createObjectURL(e.target.files[0]);
};
//canvasイメージをダウンロード
const getImage = () => {
  //fabricのアクティブを解除
  canvasFab.discardActiveObject();
  canvasFab.renderAll();
  const imageCanvas = document.getElementById("myCanvas");
  const link = document.createElement('a');
  link.href = imageCanvas.toDataURL();
  link.download = 'export_image.png';
  link.click();
};
// 特定要素の削除
const elmDelete = () => {
  //選択されているオブジェクトを削除する。
  let activeObjects = canvasFab.getActiveObjects();

  if (activeObjects != null) {
    if (confirm('選択された箇所を全て削除しますか？')) {
      activeObjects.forEach(obj => {

        //対象オブジェクトを削除
        canvasFab.remove(obj);

        //矩形のIDとcanvasFab.item紐づけ用配列も削除する。
        let arrIndex = arrayRect.indexOf(obj.id);
        arrayRect.splice(arrIndex, 1);
      });
    }
  } else {
    alert("オブジェクトが選択されていません。");
  }
};
const postText = () => {
  const element = new fabric.Textbox("テキスト", {
    left: (canvasFab.width / 2),        //左
    top: (canvasFab.height / 2),
    originX: "center",
    originY: "center",
    fill: "black",
    fontSize: 64,
    fontFamily: "Ariel",
  });
  element.center();
  canvasFab.add(element);
}
</script>
<template>
  <div class="m-2">
    <h1 class="flex-none text-center">Fabric JS</h1>
    <div class="grid grid-cols-2">
      <div class="flex justify-center w-full m-2">
        <canvas id="myCanvas" width=512 height=512 class="block border-2 bg-gray400"></canvas>
      </div>
      <div>
        <div class="flex justify-center">
          <label for="loadFile" class="">Uploading Image</label>
          <input type="file" id="loadFile" v-on:change="loadFile" required minlength="4" maxlength="8" size="10">
        </div>
        <div class="flex justify-center">
          <button type="button" v-on:click="getImage" class="p-2 border-2">Capture</button>
          <button type="button" v-on:click="elmDelete" class="p-2 border-2">Delete</button>
          <button type="button" v-on:click="postText" class="p-2 border-2">DrawText</button>
        </div>
      </div>
    </div>
  </div>
</template>