<script>

const focus = {
    mounted(el) {
        el.focus();
    }
}
export default {
    props: {
        subTask: Object,
    },
    emits: [
        'changeEditSubTask',
        'deleteSubTask',
        'createSubTask'
    ],
    directives: {
        focus
    },
    methods: {
        createSubTaskByEnter() {
            this.$emit('changeEditSubTask');
            this.$emit('createSubTask');
        }
    }
}

</script>

<template>

<div class="container-task sub-task" v-if="!subTask.isEdit">
    <input type="checkbox" :id="subTask.value + subTask.id"
    v-model="subTask.isDone">
    <p @click="$emit('changeEditSubTask')" 
    class="sub-task-text"
    :class="{ done: subTask.isDone}">{{ subTask.value }}</p>
    <img 
    src="./icons/delete.svg"
    class="type-icon control"
    title="Удалить"
    @click="$emit('deleteSubTask')">
</div>
<div class="container-task sub-task" v-if="subTask.isEdit">
    <input type="text" :id="subTask.id"
    v-model="subTask.value"
    v-focus
    @keypress.enter="createSubTaskByEnter()">
    <img 
    src="./icons/delete.svg"
    class="type-icon control"
    title="Удалить"
    @click="$emit('deleteSubTask')">
    <img 
    src="./icons/save.svg"
    class="type-icon control"
    title="Сохранить"
    @click="$emit('changeEditSubTask')">
</div>

</template>

<style>

p.sub-task-text {
    flex-grow: 5;
    max-width: none;
}

</style>