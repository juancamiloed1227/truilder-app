<template>
    <CodeVisualizer @close="showCode = false" v-show="showCode" :pythonCode="pythonCode"/>
    <div class="container">
        <div class="zoom-container">
            <button class="button zoom" @click="moreZoom">+</button>
            <button class="button zoom" @click="lessZoom">-</button>    
        </div>
        <div></div>
        <div class="buttons-container">
            <button class="button export">Export</button>
            <button class="button import">Import</button>
            <button class="button play" @click="play">Play</button>
        </div>
    </div>
</template>

<script setup>
import { inject, ref } from 'vue';
import CodeVisualizer from './CodeVisualizer.vue'

const editor = inject('editor')

let pythonCode = ref('');
const showCode = ref(false);


const play = () => {
    const data =  editor.value.drawflow.drawflow.Home.data
    console.log(data)
    const keys = Object.keys(data)
    let nodesOrder = Object.keys(data)
    .map(function(key) {
        return data[key];
    })
    .sort((a, b) => (a.pos_x > b.pos_x) ? 1 : (b.pos_x > a.pos_x) ? -1 : 0);
    
    pythonCode.value = '';
    nodesOrder.forEach(element => {
        (element.data['python'] && !element.data.python.includes('undefined') && element.data.convertible) ? pythonCode.value += `${element.data.python}\n` : {} 
    });

    showCode.value = true;
    console.log(pythonCode.value)
}

const moreZoom = () => editor.value.zoom_in()
const lessZoom = () => editor.value.zoom_out()
</script>

<style scoped>
button {
    font-size: 1rem;
    height: 100%;
    min-width: 40px;
    border: none;
    border-radius: 110px;
    color: white;
    font-family: 'Lato', sans-serif;
    font-weight: 400;
}
.container {
    padding: 8px;
    width: 100%;
    height: 10%;
    border-bottom: 1px solid #8f8f8f;
    display: grid;
    grid-template-columns: 2fr 2fr 3fr;
    column-gap: 10px;
}
.buttons-container {
    text-align: right;
    display: grid;
    grid-template-columns: 1fr 1fr 2fr;
    column-gap: 15px;
}
.play {
    background-color: #52ff00;
}
.play:hover {
    background-color: #42ce02;
}
.import {
    background-color: #ff0000;
}
.import:hover {
    background-color: #cb0000;
}
.export {
    background-color: #4690ff
}
.export:hover {
    background-color: #196deb
}
.zoom {
    background-color: #5f5f5f;
    margin-right: 10px;
}
</style>