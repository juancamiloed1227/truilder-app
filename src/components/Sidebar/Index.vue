<template>
    <div>
        <div v-show="!showNodeList">
            <ul v-for="item in categories" :key="item.category">
                <li>
                    <Category 
                        @showNodeSelector = "showHideNodes(item.category)"
                        :category="item.category"
                        :color="item.color"
                    />
                </li>
            </ul>
        </div>
            <NodeSelector v-show="showNodeList" @close="(showHideNodes(''))">
                <template #node-list>
                    <NodeList :nodeType="nodeType"/>
                </template>
            </NodeSelector>
        </div>
</template>

<script setup>
import { ref } from 'vue';
import Category from './Category.vue';
import NodeSelector from './NodeSelector.vue';
import NodeList from '@/components/Sidebar/NodeList.vue';

const categories = [
    {
        category: 'Variables',
        color: '5100ff',
    },
    {
        category: 'Math',
        color: 'e07558',
    },
    {
        category: 'Logic',
        color: 'e0d74c',
    },
    {
        category: 'Methods',
        color: '79e0c2',
    },
]

const showNodeList = ref(false);
let nodeType = ref('');

const showHideNodes = type => {
    showNodeList.value = !showNodeList.value;
    nodeType.value = type
}
</script>

<style scoped>
ul {
    padding: 15px;
}
</style>
