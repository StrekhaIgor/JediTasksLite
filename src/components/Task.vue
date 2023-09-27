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
        'generateStartSubTask'
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
                this.$emit('setExecuteDate', this.taskListId, this.task.id, new Date(executeDate));
            }
        },
        message() {
            return "Создайте задачу для проекта " + this.task.value;
        },
        isDone() {
            return this.task.isDone;
        },
        executeDate() {
            let today = new Date().toLocaleDateString();
            if (this.task.executeDate.toLocaleDateString() === today) {
                return 'Сегодня';
            } else {
                return this.task.executeDate.toLocaleDateString();
            }
        }
    },
    methods: {
        changeEditTask() {
            this.$emit('changeEditTask');
            if (this.taskListId === 2) {
                this.$emit('generateStartSubTask', this.task.id, this.message);
            }
        },
        delayDelete() {
            setTimeout(() => this.$emit('deleteTask'), 1000);
        }
    },
    watch: {
        isDone() {
            if (this.task.isDone) this.delayDelete();
        }
    }
}

</script>

<template>

<div class="container-task" v-if="!task.isEdit">
    <input type="checkbox" v-model="task.isDone">
    <div class="task-date-wrapper">
        <p @click="$emit('changeEditTask')" 
        :class="{ done: task.isDone}">{{ task.value }}</p>
        <p v-if="this.task.executeDate">
            {{ this.executeDate }}
        </p>
    </div>
    <button @click="$emit('moveTaskToProjects')" v-if="taskListId !== 2">В проекты</button>
    <button v-else @click="this.$emit('changeVisibleSubTasks', task.id)">Показать задачи</button>
</div>
<div class="container-task" v-if="task.isEdit">
    <input type="checkbox" v-model="task.isDone">
    <input type="text" v-model="task.value" 
    @blur="changeEditTask" 
    @keyup.enter="changeEditTask">
    <input type="date" v-model="this.date">
    <button @click="changeEditTask">Сохранить</button>
</div>
</template>

<style>

div.container-task {
    display: flex;
    flex-direction: row;
    width: 50vw;
    border: 1px solid black;
}

div.task-date-wrapper {
    display: flex;
    flex-direction: column;
    flex-grow: 1;
}

p {
    padding: 5px 10px;
}

p.done {
    text-decoration: line-through;
}

</style>