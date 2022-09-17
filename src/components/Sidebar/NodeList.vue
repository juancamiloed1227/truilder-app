<template>
    <div class="scroller">
        <div class="list" v-if="nodeType == 'Variables'">
            <NodeNumber @addNode="addNodeToBoard"/>
        </div>
        <div class="list" v-else-if="nodeType == 'Math'">
            <NodeAdd @addNode="addNodeToBoard"/>
            <NodeSubtract @addNode="addNodeToBoard"/>
            <NodeMultiply @addNode="addNodeToBoard"/>
            <NodeDivide @addNode="addNodeToBoard"/>
        </div>
        <div class="list" v-else-if="nodeType == 'Logic'">
            <NodeBlock @addNode="addNodeToBoard" />
            <NodeIf @addNode="addNodeToBoard"/>
            <NodeFor @addNode="addNodeToBoard"/>
        </div>
        <div class="list" v-else-if="nodeType == 'Methods'">
            <NodePrint @addNode="addNodeToBoard"/>
        </div>
    </div>
</template>

<script setup>
import { defineProps, inject, ref } from 'vue';
import NodeNumber from '../Board/Nodes/NodeNumber.vue'
import NodeAdd from '../Board/Nodes/NodeAdd.vue';
import NodeSubtract from '../Board/Nodes/NodeSubtraction.vue';
import NodeMultiply from '../Board/Nodes/NodeMultiply.vue';
import NodeDivide from '../Board/Nodes/NodeDivide.vue'
import NodePrint from '../Board/Nodes/NodePrint.vue';
import NodeIf from '../Board/Nodes/NodeIf.vue';
import NodeBlock from '../Board/Nodes/NodeBlock.vue';
import NodeFor from '../Board/Nodes/NodeFor.vue';

const props = defineProps({
    nodeType: {
        type: String,
        required: true,
    }
})

const randomInt = (min, max) => {
    min = Math.ceil(min);
    max = Math.floor(max);
    return Math.floor(Math.random() * (max - min) + min);
}

const editor = ref(inject('editor'));

const addNodeToBoard = (values) => {
    editor.value.addNode(values.name, values.inputs, values.outputs, randomInt(50, 800), randomInt(50, 800), values.name, {'convertible': true}, values.node, 'vue');
}
</script>

<style scoped>
.scroller {
    height: 77vh;
    overflow-y: auto;
}
.list > * {
    cursor: default;
    margin-bottom: 20px;
}
</style>