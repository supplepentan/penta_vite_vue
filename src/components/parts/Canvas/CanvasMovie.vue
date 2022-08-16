<script setup>
import { onMounted } from 'vue';
// ビデオ
var src_video;

// キャンバス
var src_canvas;
var src_ctx;

// タイマーのID
var interval_id;

onMounted(() => {
  src_canvas = document.getElementById("SrcCanvas");
  src_ctx = src_canvas.getContext("2d");

  src_video = document.getElementById("SrcVideo");
})


// 停止
function stopMovie() {
  src_video.loop = false;
  src_video.pause();
  clearInterval(interval_id);
}

// タイマーイベント
function onNotify() {
  src_ctx.drawImage(src_video, 0, 0, src_video.width, src_video.height);
}

// ユーザーによりファイルが追加された  
const onAddFile = function (event) {
  var files;
  var filename;

  if (event.target.files) {
    files = event.target.files;
  } else {
    files = event.dataTransfer.files;
  }

  if (files[0] != undefined) {
    filename = files[0].name;

    // 拡張子の取得 
    var ext = filename.split('.');
    ext = ext[ext.length - 1].toUpperCase();

    // 動画が再生できる状態になった時
    src_video.onloadeddata = function () {

      // 最大横幅を640にする
      var size = 640;
      if (src_video.videoWidth > size) {
        var aspectratio = src_video.videoWidth / size;
        var another = Math.round(src_video.videoHeight / aspectratio);

        src_video.width = size;
        src_video.height = another;
      } else {
        src_video.width = src_video.videoWidth;
        src_video.height = src_video.videoHeight;;
      }

      src_canvas.width = src_video.width;
      src_canvas.height = src_video.height;

      // タイマー発動(60fps)
      interval_id = setInterval(onNotify, 1000 / 60);

      // 再生      
      src_video.play();
    };

    src_video.onerror = function (e) {
      alert('このファイルは読み込めません。');
    };

    // 以前のURLオブジェクトを削除する
    if (src_video.src) {
      src_video.pause();
      window.URL.revokeObjectURL(src_video.src);
    }

    // MIMEを指定して動画を読み込む
    var mime = "video/mp4";
    if (ext == "WEBM") {
      mime = "video/webm";
    } else if (ext == "OGG" || ext == "OGX" || ext == "OGV") {
      mime = "video/ogg";
    }
    src_video.src = URL.createObjectURL(new Blob([files[0]], { type: mime }));
  }
  document.getElementById("inputfile").value = '';
}
</script>
<template>
  <div>
    <h4>画像の読み込み</h4>
    <input type="file" id="inputfile" accept="video/mp4,video/webm,video/ogg" @change="onAddFile">
    <p><button onclick="stopMovie(); ">停止</button></p>
    <h4>videoタグで再生</h4>
    <div class="flex">
      <video id="SrcVideo" class=""></video>
      <h4>canvasで再生</h4>
      <canvas id="SrcCanvas" class=""></canvas>
    </div>
  </div>
</template>