<template>
    <NodeTemplate @click="addNode" title="Number" firstColor='5100ff' secondColor="3a00b6">
        <template #content>
            <div ref="node" class="node">
                <input class="variable-name" type="text" placeholder="Variable name" v-model="variableName" @change="updateData"/>
                <input class="number" type="number" placeholder="0" v-model="number" @change="updateData"/>
            </div>
        </template>
    </NodeTemplate>
</template>

<script setup>
import { defineEmits, defineProps, onMounted, nextTick, ref } from 'vue';
import NodeTemplate from '@/components/Board/Nodes/NodeTemplate.vue'

const props = defineProps({
    editor: {
        type: Object,
    }
})

let node = ref(null);
let id = ref(0);

const variableName = ref()
const number = ref()

const values = {
    'name': 'number',
    'inputs': 1,
    'outputs': 1,
    'node': 'nodeNumber'
}

const emit = defineEmits(["addNode", "updateData"]);

const addNode = () => {
    emit('addNode', values);
}

const updateData = () => {
    const obj = {...props.editor.getNodeFromId(id).data}
    obj['python'] = `${variableName.value} = ${number.value}`
    obj['output'] = `${variableName.value}`
    
    props.editor.updateNodeDataFromId(id, obj)
}

onMounted(async () => {
    await nextTick();
    id = node.value.parentElement.parentElement.parentElement.id.split('-')[1];
});
</script>

<style scoped>
.number, .variable-name{
    margin: 4px 0 4px 0;
    padding: 4px 6px 4px 6px;
    border-radius: 5px;
    border: none;
}
.node {
    box-sizing: border-box;
}
.node:hover{
    cursor: move;
}
</style>