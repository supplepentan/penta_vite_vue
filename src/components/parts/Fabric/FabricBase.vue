<script setup>
//http://fabricjs.com/
//https://qiita.com/kurukuruz/items/04866272867f4ace3a17
//https://qiita.com/kurukuruz/items/966f0c6942df73f35f46
//https://erabikata.info/javascript-canvas-fabric-image.html
//https://zenn.dev/megeton/articles/aaad434c8533b6
//https://qiita.com/bstyle6130/items/22b2336893773f775cab
import { fabric } from "fabric";
import { onMounted, ref } from "vue";

var canvas;
var canvasFab;
var canvasImage;
var ctxImage;
const downloadImageType = ref();
// create a rectangle object
const rect = new fabric.Rect({
  left: 200,
  top: 100,
  fill: 'red',
  width: 31,
  height: 31,
});

onMounted(() => {
  canvas = document.getElementById("canvasFab");
  console.log(canvas.width);
  canvasFab = new fabric.Canvas("canvasFab", { preserveObjectStacking: true });
  console.log(canvas.width);
  canvasImage = document.getElementById("canvasImage");
  ctxImage = canvasImage.getContext('2d');
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
  ctxImage.drawImage(canvas, 0, 0, 512, 512);
  const link = document.createElement('a');
  if (downloadImageType.value == "Jpeg") {
    link.href = canvasImage.toDataURL("image/jpeg");
    link.download = 'export_image.jpg';
    link.click();
  } else {
    link.href = canvasImage.toDataURL("image/png");
    link.download = 'export_image.png';
    link.click();
  };
  ctxImage.clearRect(0, 0, canvasImage.width, canvasImage.height);
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
//テキスト追加
const drawText = () => {
  const element = new fabric.Textbox("Text", {
    left: (canvasFab.width / 2),
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
//ポリゴン追加
const drawPlygon = () => {
  var points = [{
    x: 0, y: 4
  }, {
    x: 16, y: 0
  }, {
    x: 32, y: 4
  }, {
    x: 32, y: 32
  }, {
    x: 16, y: 36
  }, {
    x: 0, y: 32
  }];//ポリゴンの初期位置
  var polygon = new fabric.Polygon(points, {
    left: (canvasFab.width / 2),
    top: (canvasFab.height / 2),
    originX: "center",
    originY: "center",
    fill: false,
    strokeWidth: 2,
    stroke: 'blue',
    scaleX: 4,
    scaleY: 4,
    objectCaching: false,
    transparentCorners: false,
    cornerColor: 'blue',
  });
  //canvasFab.viewportTransform = [0.7, 0, 0, 0.7, -50, 50];
  canvasFab.add(polygon);
};
// define a function that can locate the controls.
// this function will be used both for drawing and for interaction.
function polygonPositionHandler(dim, finalMatrix, fabricObject) {
  var x = (fabricObject.points[this.pointIndex].x - fabricObject.pathOffset.x),
    y = (fabricObject.points[this.pointIndex].y - fabricObject.pathOffset.y);
  return fabric.util.transformPoint(
    { x: x, y: y },
    fabric.util.multiplyTransformMatrices(
      fabricObject.canvas.viewportTransform,
      fabricObject.calcTransformMatrix()
    )
  );
};
function getObjectSizeWithStroke(object) {
  var stroke = new fabric.Point(
    object.strokeUniform ? 1 / object.scaleX : 1,
    object.strokeUniform ? 1 / object.scaleY : 1
  ).multiply(object.strokeWidth);
  return new fabric.Point(object.width + stroke.x, object.height + stroke.y);
};
// define a function that will define what the control does
// this function will be called on every mouse move after a control has been
// clicked and is being dragged.
// The function receive as argument the mouse event, the current trasnform object
// and the current position in canvas coordinate
// transform.target is a reference to the current object being transformed,
function actionHandler(eventData, transform, x, y) {
  var polygon = transform.target,
    currentControl = polygon.controls[polygon.__corner],
    mouseLocalPosition = polygon.toLocalPoint(new fabric.Point(x, y), 'center', 'center'),
    polygonBaseSize = getObjectSizeWithStroke(polygon),
    size = polygon._getTransformedDimensions(0, 0),
    finalPointPosition = {
      x: mouseLocalPosition.x * polygonBaseSize.x / size.x + polygon.pathOffset.x,
      y: mouseLocalPosition.y * polygonBaseSize.y / size.y + polygon.pathOffset.y
    };
  polygon.points[currentControl.pointIndex] = finalPointPosition;
  return true;
};
// define a function that can keep the polygon in the same position when we change its
// width/height/top/left.
function anchorWrapper(anchorIndex, fn) {
  return function (eventData, transform, x, y) {
    var fabricObject = transform.target,
      absolutePoint = fabric.util.transformPoint({
        x: (fabricObject.points[anchorIndex].x - fabricObject.pathOffset.x),
        y: (fabricObject.points[anchorIndex].y - fabricObject.pathOffset.y),
      }, fabricObject.calcTransformMatrix()),
      actionPerformed = fn(eventData, transform, x, y),
      newDim = fabricObject._setPositionDimensions({}),
      polygonBaseSize = getObjectSizeWithStroke(fabricObject),
      newX = (fabricObject.points[anchorIndex].x - fabricObject.pathOffset.x) / polygonBaseSize.x,
      newY = (fabricObject.points[anchorIndex].y - fabricObject.pathOffset.y) / polygonBaseSize.y;
    fabricObject.setPositionByOrigin(absolutePoint, newX + 0.5, newY + 0.5);
    return actionPerformed;
  }
};
function drawPlygonEdit() {
  // clone what are you copying since you
  // may want copy and paste on different moment.
  // and you do not want the changes happened
  // later to reflect on the copy.
  var poly = canvasFab.getObjects()[0];
  canvasFab.setActiveObject(poly);
  poly.edit = !poly.edit;
  if (poly.edit) {
    var lastControl = poly.points.length - 1;
    poly.cornerStyle = 'circle';
    poly.cornerColor = 'rgba(0,0,255,0.5)';
    poly.controls = poly.points.reduce(function (acc, point, index) {
      acc['p' + index] = new fabric.Control({
        positionHandler: polygonPositionHandler,
        actionHandler: anchorWrapper(index > 0 ? index - 1 : lastControl, actionHandler),
        actionName: 'modifyPolygon',
        pointIndex: index
      });
      return acc;
    }, {});
  } else {
    poly.cornerColor = 'blue';
    poly.cornerStyle = 'rect';
    poly.controls = fabric.Object.prototype.controls;
  }
  poly.hasBorders = !poly.edit;
  canvasFab.requestRenderAll();
};

</script>
<template>
  <div class="m-2">
    <h1 class="text-center">Fabric JS</h1>
    <div class="grid grid-flow-col">
      <div class="relative flex justify-center bg-sky-100">
        <canvas id="canvasFab" width="512" height="512" class="absolute border-2 bg-gray400 "></canvas>
        <canvas id="canvasImage" width="512" height="512" class="absolute invisible"></canvas>
      </div>
      <div class="bg-gray-100 ">
        <p>Layer</p>
      </div>
      <div class="bg-gray-300 ">
        <div class="border">
          <label for="loadFile" class="">Uploading Image</label><br />
          <input type="file" id="loadFile" v-on:change="loadFile" required minlength="4" maxlength="8" size="10">
        </div>
        <div class="border">
          <button type="button" v-on:click="elmDelete" class="p-2 border-2">Delete</button>
        </div>
        <div class="border">
          <p>Add Object</p>
          <button type="button" v-on:click="drawText" class="p-2 border-2">Text</button>
          <button type="button" v-on:click="drawPlygon" class="p-2 border-2">Polygon</button>
          <button type="button" v-on:click="drawPlygonEdit" class="p-2 border-2">Polygon Edit</button>
        </div>
        <div class="border">
          <p>Download</p>
          <div>
            <input type="radio" id="imageJpeg" value="Jpeg" v-model="downloadImageType">
            <label for="one">Jpeg</label>
            <br>
            <input type="radio" id="imagePing" value="Ping" v-model="downloadImageType">
            <label for="two">Ping</label>
            <br>
          </div>
          <div>
            <button type="button" v-on:click="getImage" class="p-2 border-2">Download</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>

</style>