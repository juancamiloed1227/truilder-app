<template>
    <NodeTemplate @click="addNode" title="Block of code" firstColor='e0d74c' secondColor="ffef00">
        <template #content>
            <div ref="node" class="node">
                <button @click="addOutput">Add step</button>
                <button @click="removeOutput">Remove step</button>
            </div>
        </template>
    </NodeTemplate>
</template>

<script setup>
import { defineEmits, defineProps, onMounted, ref, nextTick } from 'vue';
import NodeTemplate from '@/components/Board/Nodes/NodeTemplate.vue'

const values = {
    'name': 'block',
    'inputs': 1,
    'outputs': 1,
    'node': 'nodeBlock'
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

const addOutput = () => props.editor.addNodeOutput(id)
const removeOutput = () => {
    const node = props.editor.getNodeFromId(id)
    const keys = Object.keys(node.outputs);
    props.editor.removeNodeOutput(id, keys[keys.length-1])
}

onMounted(async () => {
    await nextTick();
    id = node.value.parentElement.parentElement.parentElement.id.split('-')[1];
    props.editor.on('connectionCreated', connection => connectionCreated(connection))
    props.editor.on('nodeSelected', id => nodeSelected(id))
    props.editor.on('nodeUnselected', function() {
        const node_outputs = props.editor.getNodeFromId(id).outputs;
        const keys = Object.keys(node_outputs);

        for(let i = 0; i < keys.length; i++){
            const key = keys[i]
            node_outputs[key].connections.forEach(connection => {
                const obj = {'input_class': connection.output, 'input_id': connection.node, 'output_class': key, 'output_id': id}
                connection.node == selectedNode.value ? connectionCreated(obj) : {}
            });
        }
    })
});

const connectionCreated = (connection) => {
    if(connection.output_id == id){
        const input_node = props.editor.getNodeFromId(connection.input_id)
        const output_node = props.editor.getNodeFromId(connection.output_id)
        input_node.class == 'add' && Object.keys(input_node.inputs).length == 2 ? props.editor.addNodeInput(connection.input_id) : {}
        
        const obj = {...output_node.data}
        if (input_node.data.python != undefined){
            const output_class = connection.output_class
            obj[output_class] = input_node.data.python
            props.editor.updateNodeDataFromId(id, obj)

            const input_data = {...input_node.data}
            input_data['convertible'] = false
            props.editor.updateNodeDataFromId(connection.input_id, input_data)
        }
        
        let pythonCode = ''
        const keys = Object.keys(obj)
        for(let i = 0; i < keys.length; i++){
            const key = keys[i]
            if(key != 'python' && key != 'convertible'){
                i != keys.length -1 ? pythonCode += `${obj[key]}\n` : pythonCode += `${obj[key]}`
            }
        }
        obj['python'] = pythonCode
    }
    if(connection.input_id == id){
        const obj = {...props.editor.getNodeFromId(id).data}
        obj['convertible'] = false
        props.editor.updateNodeDataFromId(id, obj)
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