<template>
    <div class="body">
        <div class="header">
            <slot name="header"></slot>
        </div>    
        <div class="content">
            <div class="sidebar">
                <slot name="sidebar"></slot>
            </div>
            <div class="drawflow-board">
                <slot name="board-controller"></slot>
                <slot name="drawflow-board"></slot>
            </div>
        </div>
    </div>
</template>

<style>
.body {
    height: 100vh;
    width: 100vw;
}
.header {
    height: 10%;
    border-bottom: 1px solid #8f8f8f;
}
.content {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: stretch;
    height: 90%;
}
.sidebar {
    width: 20%;
    border-right: 1px solid #8f8f8f;
}
.drawflow-board {
    flex: 1;
}
</style>