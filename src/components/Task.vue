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
        'generateStartSubTask',
        'setTypeTask'
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
        },
        srcIconTypeTask() {
            switch(this.task.typeTask) {
                case 'home':
                    return '/src/components/icons/homeTask.svg';
                case 'hobbie':
                    return '/src/components/icons/hobbieTask1.svg';
                case 'job':
                    return '/src/components/icons/jobTask.svg';
                default: return '';
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
    <div class="task-date-wrapper"
    @click="$emit('changeEditTask')">
        <p 
        :class="{ done: task.isDone}">{{ task.value }}</p>
        <p v-if="this.task.executeDate">
            {{ this.executeDate }}
        </p>
    </div>
    <div class="container-type-task-icon"
    @click="$emit('changeEditTask')">
        <img :src="srcIconTypeTask" 
        class="type-icon picked" 
        v-if="this.task.typeTask"
        :title="this.task.typeTask">
    </div>
    <button 
    @click="$emit('moveTaskToProjects')" 
    v-if="taskListId !== 2"
    class="button-to-project"
    >В проекты</button>
    <button 
    v-else 
    @click="this.$emit('changeVisibleSubTasks', task.id)"
    >Показать задачи</button>
</div>
<div class="container-task" v-if="task.isEdit">
    <input type="checkbox" v-model="task.isDone">
    <div class="task-date-wrapper">
        <input type="text" v-model="task.value" 
        @keyup.enter="changeEditTask">
        <input type="date" v-model="this.date">
    </div>
    <div class="container-type-task-icon">
        <img src="/src/components/icons/jobTask.svg"
        @click="this.$emit('setTypeTask', this.taskListId, task.id, 'job')" 
        class="type-icon"
        :class="{picked: this.task.typeTask === 'job'}"
        title="job">
        <img src="/src/components/icons/homeTask.svg"
        @click="this.$emit('setTypeTask', this.taskListId, task.id, 'home')" 
        class="type-icon"
        :class="{picked: this.task.typeTask === 'home'}"
        title="home">
        <img src="/src/components/icons/hobbieTask1.svg"
        @click="this.$emit('setTypeTask', this.taskListId, task.id, 'hobbie')"
        class="type-icon"
        :class="{picked: this.task.typeTask === 'hobbie'}"
        title="hobbie">
    </div>
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
}

div.container-type-task-icon {
    flex-grow: 1;
}

p {
    padding: 5px 10px;
}

p.done {
    text-decoration: line-through;
}

img.type-icon {
    width: 30px;
    height: 30px;
    margin: 2px;
    padding: 5px;
    border-radius: 5px;
    border: 1px solid black;
}

img.picked {
    background-color: greenyellow;
}

button {
    height: max-content;
}

</style>