<template>
    <NodeTemplate @click="addNode" title="Print" firstColor='79E0C2' secondColor="00E5A3">
        <template #content>
            <div ref="node" class="node">
                <input @change="updateData" type="text" v-model="text" placeholder="Text(optional)">
                <input @change="updateData" type="text" v-model="variable" placeholder="Some Variable(optional)"/>
            </div>
        </template>
    </NodeTemplate>
</template>

<script setup>
import { defineEmits, defineProps, onMounted, ref, nextTick} from 'vue';
import NodeTemplate from '@/components/Board/Nodes/NodeTemplate.vue'

const values = {
    'name': 'print',
    'inputs': 1,
    'outputs': 0,
    'node': 'nodePrint'
}

const props = defineProps({
    editor: {
        type: Object,
    }
})

let node = ref(null);
let id = ref(0);

const text = ref()
const variable = ref()

const emit = defineEmits(["addNode"]);
const addNode = () => emit('addNode', values)

const updateData = () => {
    const data = props.editor.getNodeFromId(id).data
    const obj = {...data}
    obj['python'] = text != undefined && variable != undefined ? `print('${text.value}', ${variable.value})` : text != undefined && variable == undefined ? `print('${text.value}')` : text == undefined && variable != undefined ? `print(${variable.value})` : `print('')`
    obj['output'] = text != undefined && variable != undefined ? `print('${text.value}', ${variable.value})` : text != undefined && variable == undefined ? `print('${text.value}')` : text == undefined && variable != undefined ? `print(${variable.value})` : `print('')`
    props.editor.updateNodeDataFromId(id, obj)
}

onMounted(async () => {
    await nextTick();
    id = node.value.parentElement.parentElement.parentElement.id.split('-')[1];
});
</script>

<style>
.node {
    box-sizing: border-box;
}
.node:hover{
    cursor: move;
}
input {
    margin: 4px 0 4px 0;
    padding: 4px 6px 4px 6px;
    border-radius: 5px;
    border: none;
}
</style>