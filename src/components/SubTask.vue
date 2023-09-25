<script>
export default {
    data() {
        return {
            date: new Date,
        }
    },
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

<div class="container-task" v-if="!subTask.isEdit">
    <input type="checkbox" v-model="subTask.isDone">
    <p @click="$emit('changeEditSubTask')" 
    :class="{ done: subTask.isDone}">{{ subTask.value }}</p>
    <button @click="$emit('deleteSubTask')">Удалить</button>
</div>
<div class="container-task" v-if="subTask.isEdit">
    <input type="text" v-model="subTask.value">
    <input type="date" v-model="this.date">
    <button @click="$emit('deleteTask')">Удалить</button>
    <button @click="$emit('changeEditSubTask')">Сохранить</button>
</div>

</template>

<style>

</style>