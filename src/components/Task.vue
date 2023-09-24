<script>
export default {
    props: {
        task: Object,
        taskListId: Number,
    },
    emits: [
        'deleteTask', 
        'changeEditTask', 
        'moveTaskToProjects',
        'setExecuteDate',
        'changeVisibleSubTasks',
        'createStartTaskOfProject'
    ],
    computed: {
        date: {
            get() {
                if (this.task.executeDate) {
                    return this.task.executeDate;
                } else {
                    return new Date;
                }
            },
            set(executeDate) {
                this.$emit('setExecuteDate', this.taskListId, this.task.id, executeDate);
            }
        }
    },
    mounted() {
        console.log(this.task);
        if (this.taskListId !== 2) return;
        if (this.task.subTasks.length === 0) {
            let message = 'Добавьте задачу для проекта ' + this.task.value;
            let newTask = {
                value: message,
                date: new Date,
                isDone: false,
                isEdit: false,
            };
            this.$emit('createStartTaskOfProject', newTask);
            console.log('work')
        }
    }
}

</script>

<template>

<div class="container-task" v-if="!task.isEdit">
    <input type="checkbox" v-model="task.isDone">
    <p @click="$emit('changeEditTask')" 
    :class="{ done: task.isDone}">{{ task.value }}</p>
    <button @click="$emit('deleteTask')">Удалить</button>
    <button @click="$emit('moveTaskToProjects')" v-if="taskListId !== 2">В проекты</button>
    <button v-else @click="this.$emit('changeVisibleSubTasks', task.id)">Показать задачи</button>
</div>
<div class="container-task" v-if="task.isEdit">
    <input type="checkbox" v-model="task.isDone">
    <input type="text" v-model="task.value" 
    @blur="$emit('changeEditTask')" 
    @keyup.enter="$emit('changeEditTask')">
    <input type="date" v-model="this.date">
    <button @click="$emit('deleteTask')">Удалить</button>
    <button @click="$emit('changeEditTask')">Сохранить</button>
</div>
</template>

<style>

div.container-task {
    display: flex;
    flex-direction: row;
    width: 50vw;
    border: 1px solid black;
}

p {
    padding: 5px 10px;
}

p.done {
    text-decoration: line-through;
}

</style>