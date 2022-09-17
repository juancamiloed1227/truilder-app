<template>
  <div id="drawflow"></div>
</template>

<script setup>

import Drawflow from 'drawflow'
import { onMounted, h, getCurrentInstance, render, inject, ref } from 'vue'
import NodeNumber from './Nodes/NodeNumber';
import NodeAdd from './Nodes/NodeAdd.vue';
import NodeSubtract from './Nodes/NodeSubtraction.vue';
import NodeMultiply from './Nodes/NodeMultiply.vue';
import NodeDivide from './Nodes/NodeDivide.vue';
import NodePrint from './Nodes/NodePrint.vue';
import NodeIf from './Nodes/NodeIf.vue';
import NodeBlock from './Nodes/NodeBlock.vue';
import NodeFor from './Nodes/NodeFor.vue';

const editor = inject('editor');
const Vue = { version: 3, h, render };
const instance = getCurrentInstance();

let selectedNode = ref(0);

onMounted(() => {
    const id = document.getElementById("drawflow");
    editor.value = new Drawflow(id, Vue, instance.appContext.app._context);
    editor.value.start();

    editor.value.reroute = true;

    editor.value.on('connectionCreated', connection => connectionCreated(connection))
    editor.value.on('connectionRemoved', connection => connectionRemoved(connection))
    editor.value.on('nodeSelected', id => nodeSelected(id))
    editor.value.on('nodeUnselected', function() {
      const node_outputs = editor.value.getNodeFromId(selectedNode.value).outputs
      const keys = Object.keys(node_outputs)

      for(let i = 0; i < keys.length; i++){
        let key = keys[i]
        
        node_outputs[key].connections.forEach(connection => {
          let obj = {...editor.value.getNodeFromId(connection.node).data};
          obj[connection.output] = editor.value.getNodeFromId(selectedNode.value).data.output;
          editor.value.updateNodeDataFromId(connection.node, obj)
        }); 
      }
    })

    editor.value.registerNode('nodeNumber', NodeNumber, { 'editor': editor.value }, {});
    editor.value.registerNode('nodeAdd', NodeAdd, { 'editor': editor.value }, {});
    editor.value.registerNode('nodeSubtract', NodeSubtract, { 'editor': editor.value }, {});
    editor.value.registerNode('nodeMultiply', NodeMultiply, { 'editor': editor.value }, {});
    editor.value.registerNode('nodeDivide', NodeDivide, { 'editor': editor.value }, {});
    editor.value.registerNode('nodePrint', NodePrint, { 'editor': editor.value }, {});
    editor.value.registerNode('nodeIf', NodeIf, { 'editor': editor.value }, {});
    editor.value.registerNode('nodeBlock', NodeBlock, { 'editor': editor.value }, {});
    editor.value.registerNode('nodeFor', NodeFor, { 'editor': editor.value }, {});

    editor.value.addNode('number', 1, 1, 100, 100, 'number', {'convertible': true}, 'nodeNumber', 'vue');
});

const connectionCreated = (connection) => {
  const inputNode  = editor.value.getNodeFromId(connection.input_id)
  const outputNode = editor.value.getNodeFromId(connection.output_id)

  const input_class = connection.input_class
  const output = outputNode.data.output

  const obj = {...inputNode.data }
  obj[input_class] = output

  editor.value.updateNodeDataFromId(connection.input_id, obj)
}

const connectionRemoved = (connection) => {
  const obj = {...editor.value.getNodeFromId(connection.input_id).data}
  const input_class = connection.input_class

  delete obj[input_class]
  editor.value.updateNodeDataFromId(connection.input_id, obj)
}

const nodeSelected = (id) => selectedNode.value = id
</script>

<style scoped>
#drawflow {
  width: 100%;
  height: 90%;
  background-size: 20px 20px;
  background-image: radial-gradient(#bebebe 1px, transparent 1px);
}
</style>