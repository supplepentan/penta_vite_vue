<script setup>
//https://docs.opencv.org/3.4/d0/d84/tutorial_js_usage.html
//https://fujiya228.com/opencv-js/
//https://qiita.com/yoshimi0227/items/932102201299c418a2d3
//https://circleken.net/2020/10/post30/
const fileSelect = async (e) => {
  const img = new Image();
  img.onload = function () {
    let mat = cv.imread(this);

    //ダイレクト
    cv.imshow("canvas01", mat);
    //グレースケール
    let out = new cv.Mat();//out:画像処理後の画像を入れるための変数
    cv.cvtColor(mat, out, cv.COLOR_RGBA2GRAY, 0);

    //const gray = cv.cvtColor(mat, cv.COLOR_BGR2GRAY)
    cv.imshow("canvas02", out);

    //解放
    mat.delete();
    out.delete();
  };
  img.src = URL.createObjectURL(e.target.files[0]);
}
</script>
<template>
  <div>
    <h1>OpenCV</h1>
    <input type="file" accept="image/jpg, image/png" @change="fileSelect">
    <canvas id="canvas01"></canvas>
    <canvas id="canvas02"></canvas>
    <canvas id="canvas03"></canvas>
    <canvas id="canvas04"></canvas>
    <canvas id="canvas05"></canvas>
  </div>
</template>