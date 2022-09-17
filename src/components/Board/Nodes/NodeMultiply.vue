<template>
    <NodeTemplate @click="addNode" title="Multiply" firstColor='e07558' secondColor="e04c24">
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
    'name': 'multiply',
    'inputs': 2,
    'outputs': 1,
    'node': 'nodeMultiply'
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
    variableName.value != undefined ? obj['variable'] = variableName.value : {}

    const number1 = obj['number1']
    const number2 = obj['number2']
    obj['python'] = `${variableName.value} = ${number1} * ${number2}`
    props.editor.updateNodeDataFromId(id, obj)
}

onMounted(async () => {
    await nextTick();
    id = node.value.parentElement.parentElement.parentElement.id.split('-')[1];
    props.editor.on('connectionCreated', connection => connectionCreated(connection))

    const data = props.editor.getNodeFromId(id).data
    data.variable != undefined ? variableName.value = data.variable : {}
});

const connectionCreated = (connection) => {
    if(connection.input_id == id) {
        const input_node = props.editor.getNodeFromId(id)
        const output_node = props.editor.getNodeFromId(connection.output_id)
        let obj = {...input_node.data};

        if(output_node.class == 'number') {
            if(Object.keys(input_node.inputs).length == 2){
                obj['number1'] = obj['input_1']
                obj['number2'] = obj['input_2']
            }else {
                obj['number1'] = obj['input_2']
                obj['number2'] = obj['input_3']
            }
        }

        props.editor.updateNodeDataFromId(id, obj)
        updateData()
    }
}
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