<template>
    <div class="node-template" :style="style">
        <p>{{ props.title }}</p>
        <slot name="content"></slot>
    </div>
</template>

<script setup>
import { defineProps, computed } from 'vue';

const props = defineProps({
    title: {
        type: String,
        required: true,
    },
    firstColor: {
        type: String,
        required: true,
    },
    secondColor: {
        type: String,
        required: true,
    }
})


const style = computed(() => {
    return `background: #${props.firstColor}; background: linear-gradient(45deg, #${props.firstColor}, #${props.secondColor});`;
}) 

</script>

<style scoped>
div {
    padding: 20px 10px 30px 10px;
    border-radius: 10px;
    width: 180px;
    border: 1.5px solid #222222;
    cursor: move;
}
p {
    color: white;
    margin-bottom: 15px;
    font-size: 1.1em;
}
</style>