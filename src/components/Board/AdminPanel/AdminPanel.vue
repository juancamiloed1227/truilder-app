<template>
    <div class="global-container">
        <img src="@/assets/images/close-icon.svg" @click="close"/>
        <div class="flows-container" >
            <FlowSelector @close="close" v-for="item in props.databaseFlows.queryFlow" :key="item.id" :title="item.title" :id="item.id" @click="loadFlow(item.id, item.title, item.content)"/>
        </div>
    </div>
</template>

<script setup>
import { defineEmits, defineProps, inject } from 'vue';
import FlowSelector from './FlowSelector.vue'

const emit = defineEmits(["close"])
const editor = inject('editor')
let flowId = inject('flowId')
let flowTitle = inject('flowTitle')

const close = () => emit('close')

const props = defineProps({
    databaseFlows: {
        type: Object
    }
})

const loadFlow = (id, title, content) => {
    console.log('Cargar')
    flowId.value = id
    flowTitle.value = title
    
    let data = content
    data = data.replaceAll('closeSquare', '}')
    data = data.replaceAll('openSquare', '{')
    data = data.replaceAll('quote', '"')
    data = data.replace(/[\r\n]/gm, 'linebreak');

    editor.value.import(JSON.parse(data))

    const obj = editor.value.drawflow.drawflow.Home.data
    const keys = Object.keys(obj)
    for(let i = 0; i < keys.length; i++){
        const key = keys[i]
        Object.entries(obj[key].data).forEach(([keyI, value]) => {
            typeof value === 'string' ? obj[key].data[keyI] = value.replaceAll('linebreak', '\n') : {}
        });
    }

    emit('close')
}
</script>

<style scoped>
.global-container {
    width: 100%;
    height: 100%;
    padding: 15px;
}
.flows-container {
    margin-top: 15px;
    height: 90%;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    column-gap: 15px;
    row-gap: 15px;
    overflow-y: auto;
}
</style>