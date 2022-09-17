<template>
    <div class="container">
        <img @click="deleteFlow" class="trash-icon" src="@/assets/images/trash-icon.svg" />
        <p>{{props.title}}</p>
    </div>
</template>

<script setup>
import { defineProps, inject, defineEmits } from 'vue';

let flowId = inject('flowId')

const props = defineProps({
    title: {
        type: String,
        required: true
    },
    id: {
        type: String,
        required: true
    }
})

const emit = defineEmits(['close'])

const deleteFlow = (e) => {
    e ? e.stopPropagation() : {}
    
    flowId.value = undefined
    
    fetch(`http://localhost:3000/flows/${props.id}`, {
            method: "DELETE",
            mode: 'cors'            
        })
        .then(response => response.json()) 
        .then(json => {
            console.log(json)
        });

    emit('close')
}
</script>

<style scoped>
.container {
    width: 100%;
    height: 150px;
    border-radius: 10px;
    padding: 15px;
    background: #5100ff;
    background: linear-gradient(45deg, #5100ff, #3a00b6);
    border: black 2px solid;
    cursor: pointer;
}
.trash-icon {
    margin-bottom: 5px;
    padding: 3px;
    border-radius: 50%;
    z-index: -1;
}
.trash-icon:hover {
    background: #3b00bb;

}
p {
    color: white;
    font-size: 1.2em;
}
</style>
