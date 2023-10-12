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
    <input type="checkbox" v-model="subTask.isDone">
    <p @click="$emit('changeEditSubTask')" 
    :class="{ done: subTask.isDone}">{{ subTask.value }}</p>
    <button @click="$emit('deleteSubTask')">Удалить</button>
</div>
<div class="container-task sub-task" v-if="subTask.isEdit">
    <input type="text" v-model="subTask.value">
    <button @click="$emit('deleteSubTask')">Удалить</button>
    <button @click="$emit('changeEditSubTask')">Сохранить</button>
</div>

</template>

<style>

div.sub-task {
    width: 47vw;
}

</style>