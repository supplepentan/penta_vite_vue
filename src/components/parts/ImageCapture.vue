<script setup>
//https://www.petitmonte.com/javascript/canvas_select_range_get.html
import { onMounted, ref } from "vue";
// キャンバス
const src_canvas = ref();
const src_ctx = ref();
const rec_canvas = ref();
const rec_ctx = ref();

// イメージ
const image = ref();

// 矩形用
var MIN_WIDTH = 3;
var MIN_HEIGHT = 3;

var rect_MousedownFlg = false;
var rect_sx = 0;
var rect_sy = 0;
var rect_ex = 0;
var rect_ey = 0;


onMounted(() => {
    src_canvas.value = document.getElementById("SrcCanvas");
    src_ctx.value = src_canvas.value.getContext("2d");

    rec_canvas.value = document.getElementById("RecCanvas");
    rec_ctx.value = rec_canvas.value.getContext("2d");
    rec_canvas.value.width = rec_canvas.value.height = 1;

    image.value = document.getElementById("img_source");
})

// 色の反転
function getTurningAround(color) {

    // 灰色は白にする 
    if (color >= 88 && color <= 168) {
        return 255;
        // 色を反転する  
    } else {
        return 255 - color;
    }
}

const OnMousedown = function (event) {

    rect_MousedownFlg = true;

    // 座標を求める
    var rect = event.target.getBoundingClientRect();
    rect_sx = rect_ex = event.clientX - rect.left;
    rect_sy = rect_ey = event.clientY - rect.top;

    // 矩形の枠色を反転させる  
    var imagedata = src_ctx.value.getImageData(rect_sx, rect_sy, 1, 1);
    src_ctx.value.strokeStyle = 'rgb(' + getTurningAround(imagedata.data[0]) +
        ',' + getTurningAround(imagedata.data[1]) +
        ',' + getTurningAround(imagedata.data[2]) + ')';
    // 線の太さ                         
    src_ctx.value.lineWidth = 2;

    // 矩形の枠線を点線にする
    src_ctx.value.setLineDash([2, 3]);
}

const OnMousemove = function (event) {

    if (rect_MousedownFlg) {

        // 座標を求める
        var rect = event.target.getBoundingClientRect();
        rect_ex = event.clientX - rect.left;
        rect_ey = event.clientY - rect.top;

        // 元画像の再描画
        src_ctx.value.drawImage(image.value, 0, 0);

        // 矩形の描画
        src_ctx.value.beginPath();

        // 上
        src_ctx.value.moveTo(rect_sx, rect_sy);
        src_ctx.value.lineTo(rect_ex, rect_sy);

        // 下
        src_ctx.value.moveTo(rect_sx, rect_ey);
        src_ctx.value.lineTo(rect_ex, rect_ey);

        // 右
        src_ctx.value.moveTo(rect_ex, rect_sy);
        src_ctx.value.lineTo(rect_ex, rect_ey);

        // 左
        src_ctx.value.moveTo(rect_sx, rect_sy);
        src_ctx.value.lineTo(rect_sx, rect_ey);

        src_ctx.value.stroke();
    }
}

const OnMouseup = function (event) {

    // キャンバスの範囲外は無効にする    
    if (rect_sx === rect_ex && rect_sy === rect_ey) {
        // 初期化
        src_ctx.value.drawImage(image.value, 0, 0);
        rect_sx = rect_ex = 0;
        rect_sy = rect_ey = 0;
        rec_canvas.value.width = rec_canvas.value.height = 1;
    }

    // 矩形の画像を取得する
    if (rect_MousedownFlg) {

        // 矩形のサイズ
        rec_canvas.value.width = Math.abs(rect_sx - rect_ex);
        rec_canvas.value.height = Math.abs(rect_sy - rect_ey);

        // 指定のサイズ以下は無効にする[3x3]
        if (!(rec_canvas.value.width >= MIN_WIDTH && rec_canvas.value.height >= MIN_HEIGHT)) {
            // 初期化
            src_ctx.value.drawImage(image.value, 0, 0);
            rect_sx = rect_ex = 0;
            rect_sy = rect_ey = 0;
            rec_canvas.value.width = rec_canvas.value.height = 1;
        } else {
            // 矩形用キャンバスへ画像の転送
            rec_ctx.value.drawImage(image.value,
                Math.min(rect_sx, rect_ex), Math.min(rect_sy, rect_ey),
                Math.max(rect_sx - rect_ex, rect_ex - rect_sx), Math.max(rect_sy - rect_ey, rect_ey - rect_sy),
                0, 0, rec_canvas.value.width, rec_canvas.value.height);
        }
    }

    rect_MousedownFlg = false;
}

function onDragOver(event) {
    event.preventDefault();
}

function onDrop(event) {
    onAddFile(event);
    event.preventDefault();
}

// ユーザーによりファイルが追加された  
const onAddFile = function (event) {
    var files;
    var reader = new FileReader();

    if (event.target.files) {
        files = event.target.files;
    } else {
        files = event.dataTransfer.files;
    }

    // ファイルが読み込まれた
    reader.onload = function (event) {

        // イメージが読み込まれた
        image.value.onload = function () {
            src_canvas.value.width = image.value.width;
            src_canvas.value.height = image.value.height;

            // キャンバスに画像を描画
            src_ctx.value.drawImage(image.value, 0, 0);
        };

        // イメージが読み込めない
        image.value.onerror = function () {
            alert('このファイルは読み込めません。');
        };

        image.value.src = reader.result;
    };

    if (files[0]) {
        reader.readAsDataURL(files[0]);
        document.getElementById("inputfile").value = '';
    }
}        
</script>
<template>
    <div>
        <input type="file" id="inputfile" accept="image/jpeg,image/png,image/gif,image/bmp,image/x-icon"
            @change="onAddFile">
        <img id="img_source" style="display:none;">
        <p></p>
        <canvas id="SrcCanvas" @mousedown="OnMousedown" @mousemove="OnMousemove" @mouseup="OnMouseup"></canvas>
        <canvas id="RecCanvas"></canvas>
    </div>
</template>