<template>
    <div class="global-container">
        <img src="@/assets/images/close-icon.svg" @click="close"/>
        <div class="container">
            <div class="code-window">
                <p class="code">{{props.pythonCode}}</p>
            </div> 
            <div class="terminal-window">
                <p>{{terminal}}</p>
            </div>
        </div>
    </div>
</template>

<script setup>
import { defineProps, defineEmits, watchEffect, ref } from 'vue';

const emit = defineEmits(["close"])

const props = defineProps({
    pythonCode: {
        type: String,
        required: true
    }
})

const close = () => emit('close')
const terminal = ref('Hola');

watchEffect(() => {
    if(props.pythonCode != '' ){
        let _datos = {
            code: props.pythonCode,
        }

        fetch('http://localhost:3000/flows/execute', {
            method: "POST",
            mode: 'cors',
            body: JSON.stringify(_datos)            
        })
        .then(response => response.json()) 
        .then(json => {
            terminal.value = json.response
        });
    }
})
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Source+Code+Pro&display=swap');
.global-container {
    width: 100%;
    height: 100%;
    padding:25px;
}
img {
    color: black;
    margin-bottom: 15px;
}
.container {
    display: grid;
    grid-template-columns: 2fr 1fr;
    width: 100%;
    height: 90%;

}
.code-window {
    background-color: rgb(21, 22, 39); 
    border-radius: 10px 0 0 10px;
    padding: 20px;
    color: whitesmoke;
    line-height:30px;
}
.terminal-window {
    background-color: rgb(28, 29, 46);
    border-radius: 0 10px 10px 0;
    padding: 20px;
    color: whitesmoke;
    font-family: 'Source Code Pro', monospace;
    overflow-y: auto;
}
.code {
    white-space: pre-wrap; 
    font-family: 'Source Code Pro', monospace;
}
</style>