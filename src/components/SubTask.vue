<script>
export default {
    props: {
        subTask: Object,
    },
    emits: [
        'changeEditSubTask',
        'deleteSubTask'
    ]
}

</script>

<template>

<div class="container-task sub-task" v-if="!subTask.isEdit">
    <input type="checkbox" :id="subTask.id"
    v-model="subTask.isDone">
    <p @click="$emit('changeEditSubTask')" 
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
    @keypress.enter="$emit('changeEditSubTask')">
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


</style>