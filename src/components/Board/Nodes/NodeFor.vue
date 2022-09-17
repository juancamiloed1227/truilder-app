<template>
    <NodeTemplate @click="addNode" title="For loop" firstColor='e0d74c' secondColor="ffef00">
        <template #content>
            <div ref="node" class="node">
                <input @change="updateData" v-model="controlVariable" type="text" placeholder="Variable or number"/>
            </div>
        </template>
    </NodeTemplate>
</template>

<script setup>
import { defineEmits, defineProps, onMounted, ref, nextTick } from 'vue';
import NodeTemplate from '@/components/Board/Nodes/NodeTemplate.vue'

const values = {
    'name': 'for',
    'inputs': 1,
    'outputs': 1,
    'node': 'nodeFor'
}

const props = defineProps({
    editor: {
        type: Object,
    }
})

let node = ref(null);
let id = ref(0);

const emit = defineEmits(["addNode"]);
const addNode = () => emit('addNode', values)
let selectedNode = ref(0);

const controlVariable = ref();

const updateData = () => {
    const node = props.editor.getNodeFromId(id)
    const obj = {...node.data}
    controlVariable.value != undefined ? obj['controlVariable'] = controlVariable.value : {}
    obj['python'] = obj['loop'] ? `for i in range(${controlVariable.value}):\n  ${obj.loop}` : `for i in range(${controlVariable.value}):\n   `
    props.editor.updateNodeDataFromId(id, obj)
}

onMounted(async () => {
    await nextTick();
    id = node.value.parentElement.parentElement.parentElement.id.split('-')[1];
    props.editor.on('connectionCreated', connection => connectionCreated(connection))

    const data = props.editor.getNodeFromId(id).data
    data.controlVariable != undefined ? controlVariable.value = data.controlVariable : {}
});

const connectionCreated = (connection) => {
    if(connection.output_id == id){
        const input_node = props.editor.getNodeFromId(connection.input_id)
        const obj = {...input_node.data}
        obj['convertible'] = false
        props.editor.updateNodeDataFromId(connection.input_id, obj)
        if(obj['python']){
            const output_data = {...props.editor.getNodeFromId(id).data}
            if(output_data.python != undefined) {
                output_data['loop'] = obj.python
                output_data.python += obj.python
                props.editor.updateNodeDataFromId(id, output_data)
            } else{
                output_data['loop'] = obj.python
                props.editor.updateNodeDataFromId(id, output_data)
            }
        }
    }
} 

const nodeSelected = (id) => selectedNode.value = id
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
button {
    width: 100%;
    border: none;
    background: white;
    padding: 5px 3px 5px 3px;
    margin-bottom: 5px;
    border-radius: 10px;
    color: #222222;
}
</style>