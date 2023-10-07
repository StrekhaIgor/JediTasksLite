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
        'setTypeTask',
        'repeatTask',
        'createSubTask'
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
            return new Date(this.task.executeDate).toLocaleDateString();
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
        },
        listSubTasks() {
            return JSON.stringify(this.task.subTasks);
        },
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
        },
        listSubTasks() {
            console.log(this.task);
            if (this.task.subTasks.length === 0 && this.taskListId === 2) {
                this.$emit('generateStartSubTask', this.task.id, this.message);
            }
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
        <img v-if="this.task.isRepeat" 
        src="/src/components/icons/repeat.svg"
        class="type-icon picked">
        <button v-if="taskListId === 2"
        class="add-sub-task"
        @click.stop="this.$emit('createSubTask')">
            Добавить подзадачу
        </button>
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
        <img v-if="this.taskListId === 1" 
        src="/src/components/icons/repeat.svg"
        @click="this.$emit('repeatTask')"
        class="type-icon"
        :class="{picked: this.task.isRepeat}"
        title="repeat">
    </div>
    <button @click="changeEditTask">Сохранить</button>
</div>
</template>

<style>

div.container-task {
    display: flex;
    flex-direction: row;
    align-items: center;
    width: 50vw;
    border: 1px solid black;
}

div.task-date-wrapper {
    display: flex;
    flex-direction: column;
}

div.container-type-task-icon {
    flex-grow: 1;
    display: flex;
    align-items: center;
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

button.add-sub-task {
    display: block;
}

</style>