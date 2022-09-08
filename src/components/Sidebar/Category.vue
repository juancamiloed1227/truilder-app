<template>
    <button class="category-button" :style="style" @click="showNodeSelector">
        <p style="color: white; text-align: center; font-size: 1.3em; font-weight: 400;">{{ categoryTitle }}</p>
    </button>
</template>

<script setup>
import { computed, ref, defineProps, defineEmits } from 'vue';

const props = defineProps({
    category: {
        default: 'Category',
        required: true,
    },
        type: String,
    color: {
        type: String,
        default: 'purple',
        required: true,
    },
});

const style = computed(() => {
    return `background-color: #${props.color};`;
}) 

const categoryTitle = ref(props.category);
const colorClass = ref(props.color);

const emit = defineEmits(["showNodeSelector"])

const showNodeSelector = () => {
    emit("showNodeSelector");
}
</script>

<style scoped>
.category-button {
    width: 100%; 
    border: 1px solid #222; 
    padding: 12px 10px 12px 10px; 
    border-radius: 30px; 
}
</style>