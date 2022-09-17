<template>
    <AdminPanel @close="showAdminPanel = false" v-show="showAdminPanel" :databaseFlows="databaseFlows"/>
    <CodeVisualizer @close="showCode = false" v-show="showCode" :pythonCode="pythonCode"/>
    <div class="container">
        <div class="zoom-container">
            <button class="button zoom" @click="moreZoom">+</button>
            <button class="button zoom" @click="lessZoom">-</button>    
        </div>
        <div class="buttons-container">
            <button class="button import" @click="loadFlow">Load Flow</button>
            <button class="button export" @click="saveFlow">Save</button>
            <button class="button play" @click="play">Play</button>
            <input class="input-title" type="text" placeholder="title" v-model="flowTitle"/>
        </div>
    </div>
</template>

<script setup>
import { inject, provide, ref } from 'vue';
import CodeVisualizer from './CodeVisualizer.vue'
import AdminPanel from './AdminPanel/AdminPanel.vue'

const editor = inject('editor')

let pythonCode = ref('');
const showCode = ref(false);
const showAdminPanel = ref(false);
const databaseFlows = ref({'title': 'title', 'id': 1});

const flowId = ref()
const flowTitle = ref(`Flow #${Math.floor(Math.random() * 1000)}`)

provide('flowId', flowId)
provide('flowTitle', flowTitle)

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

const saveFlow = () => {
    let data = JSON.stringify(editor.value.export());
    data = data.replaceAll('{', 'openSquare')
    data = data.replaceAll('}', 'closeSquare')
    data = data.replaceAll('"', 'quote')
    
    let _datos = {
        title: flowTitle.value,
        content: data
    }
    
    if(flowId.value != undefined){
        fetch(`http://localhost:3000/flows/${flowId.value}`, {
            method: "PUT",
            mode: 'cors',
            body: JSON.stringify(_datos)            
        })
        .then(response => response.json()) 
        .then(json => {
            //console.log(json)
        });
    }else{
        fetch('http://localhost:3000/flows', {
            method: "POST",
            mode: 'cors',
            body: JSON.stringify(_datos)            
        })
        .then(response => response.json()) 
        .then(json => {
            //console.log(json)
        });
    }
}

const loadFlow = () => {
    fetch('http://localhost:3000/flows', {
            method: "GET",
            mode: 'cors'            
        })
        .then(response => response.json()) 
        .then(json => {
            databaseFlows.value = json
        });
    
        showAdminPanel.value = true;
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
    grid-template-columns: 2fr 3fr;
    column-gap: 10px;
}
.buttons-container {
    text-align: right;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 2fr;
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
.input-title {
    border: 1px black solid;
}
</style>