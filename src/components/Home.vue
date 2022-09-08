<template>
    <Layout>
        <template #header>
            <Header/>
        </template>
        <template #sidebar>
            <Sidebar/>
        </template>
        <template #drawflow-board>
            <Board/>
        </template>
        <template #board-controller>
            <BoardController/>
        </template>
    </Layout>
</template>

<script setup>
import Layout from '@/components/Layout.vue'
import Header from '@/components/Header/Index.vue'
import Sidebar from '@/components/Sidebar/Index.vue'
import Board from '@/components/Board/Index.vue'
import BoardController from './Board/BoardController.vue'
import { shallowRef, provide } from 'vue'

const editor = shallowRef({});

provide('editor', editor);
</script>