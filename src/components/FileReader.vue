<script setup>
import { onMounted, ref } from 'vue';
const element_file = ref();
const analy = ref();
onMounted(() => {
    element_file.value = document.getElementById("input_02_file");
});
const file_list = ref("init");
const file = ref("init");
const fileSelected = () => {
    file_list.value = element_file.value.files;
    file.value = file_list.value[0];
    const file_reader = new FileReader();
    file_reader.onload = function (e) {
        console.log("読み込み");
        var result = "";
        var ary_u8 = new Uint8Array(file_reader.result);
        var i;
        var str;
        var num = ary_u8.length;
        for (i = 0; i < num; i++) {
            if (ary_u8[i] < 0x10) {
                str = "0" + ary_u8[i].toString(16);
            } else {
                str = ary_u8[i].toString(16);
            }

            if ((i % 16) == 0) {
                result += str;
            } else if ((i % 16) == 15) {
                result += " " + str + "\n";
            } else {
                result += " " + str;
            }
        }
        analy.value = result;
    };
    file_reader.readAsArrayBuffer(file.value);
};
</script>

<template>
    <div class="container">
        <input type="file" id="input_02_file" v-on:change="fileSelected" style="width:100%; margin:0px 0px 10px 0px;" />
        結果:<br>
        <textarea id="edit_02_result" v-model="analy" style="width:100%; height:200px; margin:0px 0px 5px 0px;"
            disabled></textarea>
        <p>{{ file_list }}</p>
        <p>{{ file }}</p>
    </div>
</template>

<style scoped>
</style>
