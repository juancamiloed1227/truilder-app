<template>
    <div>
        <img src="@/assets/images/close-icon.svg" @click="close"/>
        <slot name="node-list"></slot>
    </div>
</template>

<script setup>
import { defineEmits } from 'vue';

const emit = defineEmits(["close"]);
const close = () => emit("close");
</script>

<style scoped>
div {
    padding: 15px;
    height: 100%;
}

img {
    margin-bottom: 20px;
}
</style>