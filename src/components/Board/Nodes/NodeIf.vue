<template>
    <NodeTemplate @click="addNode" title="If" firstColor='e0d74c' secondColor="ffef00">
        <template #content>
            <div ref="node" class="node">
                <select class="selector" @change="updateData" v-model="condition">
                    <option value="equal">Equal to</option>
                    <option value="greater">Greater than</option>
                    <option value="smaller">Smaller than</option>
                    <option value="custom">Custom</option>
                </select>
                <input class="variable" @change="updateData" type="text" v-model="number" placeholder="Variable or number"/>
            </div>
        </template>
    </NodeTemplate>
</template>

<script setup>
import { defineEmits, defineProps, onMounted, ref, nextTick } from 'vue';
import NodeTemplate from '@/components/Board/Nodes/NodeTemplate.vue'

const values = {
    'name': 'if',
    'inputs': 1,
    'outputs': 2,
    'node': 'nodeIf'
}

const props = defineProps({
    editor: {
        type: Object,
    }
})

let node = ref(null);
let id = ref(0);

const condition = ref("equal")
const number = ref()

const emit = defineEmits(["addNode"]);
const addNode = () => emit('addNode', values)

const updateData = () => {
    const obj = {...props.editor.getNodeFromId(id).data};
    
    obj['selection'] = condition.value
    if (condition.value == 'custom') {
        obj['condition'] = `if ${number.value}:`
        console.log(`Actualizada condicion: if ${number.value}:`)
    }

    number.value != undefined ? obj['number'] = number.value : {}
    
    props.editor.updateNodeDataFromId(id, obj)
    console.log(props.editor.getNodeFromId(id).data)
}

onMounted(async () => {
    await nextTick();
    id = node.value.parentElement.parentElement.parentElement.id.split('-')[1];

    const data = props.editor.getNodeFromId(id).data
    data.selection != undefined ? condition.value = data.selection : {}
    data.number != undefined ? number.value = data.number : {}

    props.editor.on('connectionCreated', connection => connectionCreated(connection))
});

const connectionCreated = (connection) => {
    if(connection.input_id == id){
        const obj = {...props.editor.getNodeFromId(id).data}
        obj['input_1'] = props.editor.getNodeFromId(connection.output_id).data.output
        condition.value == 'equal' ? obj['condition'] = `if ${obj.input_1} == ${number.value}:` : condition.value == 'greater' ? obj['condition']= `if ${obj.input_1} > ${number.value}:` : condition.value == 'smaller' ? obj['condition'] = `if ${obj.input_1} < ${number.value}:` : {}
        obj.yes_path != undefined && obj.no_path != undefined ? obj['python'] = `${obj.condition}\n   ${obj.yes_path}\nelse:\n   ${obj.no_path}` : obj.yes_path != undefined && obj.no_path == undefined ? obj['python'] = `${obj.condition}\n   ${obj.yes_path}\n` : {}
        props.editor.updateNodeDataFromId(id, obj)
    }else if(connection.output_id == id){
        const obj = {...props.editor.getNodeFromId(id).data}
        connection.output_class == 'output_1' ? obj['yes_path'] = props.editor.getNodeFromId(connection.input_id).data.python : obj['no_path'] = props.editor.getNodeFromId(connection.input_id).data.python
        obj.yes_path != undefined && obj.no_path != undefined ? obj['python'] = `${obj.condition}\n   ${obj.yes_path}\nelse:\n   ${obj.no_path}` : obj.yes_path != undefined && obj.no_path == undefined ? obj['python'] = `${obj.condition}\n   ${obj.yes_path}\n` : {}
        props.editor.updateNodeDataFromId(id, obj)
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
.selector {
    width:100%;
    height: 30px;
    border-radius: 5px;
}
.variable {
    width: 100%;
    height: 30px;
    border-radius: 5px;
}
p {
    padding-left: 4px;
    color: white;
}
</style>