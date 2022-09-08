<template>
    <NodeTemplate @click="addNode" title="Add" firstColor='e07558' secondColor="e04c24">
        <template #content>
            <div ref="node" class="node">
                <input class="variable-name" type="text" placeholder="Variable name" v-model="variableName" @change="updateData"/>
            </div>
        </template>
    </NodeTemplate>
</template>

<script setup>
import { defineEmits, defineProps, onMounted, ref, nextTick } from 'vue';
import NodeTemplate from '@/components/Board/Nodes/NodeTemplate.vue'

const values = {
    'name': 'add',
    'inputs': 2,
    'outputs': 1,
    'node': 'nodeAdd'
}

const props = defineProps({
    editor: {
        type: Object,
    }
})

let node = ref(null);
let id = ref(0);

const variableName = ref();

const emit = defineEmits(["addNode"]);
const addNode = () => emit('addNode', values)

const updateData = () => {
    const obj = {...props.editor.getNodeFromId(id).data};
    obj['output'] = variableName.value;
    
    const input1 = obj['input_1']
    const input2 = obj['input_2']
    obj['python'] = `${variableName.value} = ${input1} + ${input2}`
    props.editor.updateNodeDataFromId(id, obj)
}

onMounted(async () => {
    await nextTick();
    id = node.value.parentElement.parentElement.parentElement.id.split('-')[1];
});
</script>

<style scoped>
.variable-name, .number {
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
p {
    padding-left: 4px;
    color: white;
}
</style>