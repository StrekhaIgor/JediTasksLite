<script>

const focus = {
    mounted(el) {
        el.focus();
    }
}
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
        'createSubTask',
        'addNewTask',
        'goToProjects'
    ],
    directives: {
        focus
    },
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
        isDone() {
            return this.task.isDone;
        },
        executeDate() {
            let taskDate = new Date(this.task.executeDate).toLocaleDateString();
            if (taskDate === new Date().toLocaleDateString()) {
                return 'Сегодня';
            } else return taskDate;
        },
        listSubTasks() {
            return JSON.stringify(this.task.subTasks);
        },
        isShowRepeat() {
            if (!this.task.isRepeat) {
                return true
            } else {
            return this.convertDate(this.task.executeDate) 
            === this.convertDate(new Date())
            };
        },
        markedValue() {
            if (this.task.freezed) {
                return this.task.value + ' [Остановлен]';
            };
            return this.task.value;
        }
    },
    methods: {
        changeEditTask() {
            if (this.task.selfId === 0) {
                this.$emit('goToProjects');
                return;
            };
            this.$emit('changeEditTask');
            if (this.taskListId === 2 && this.task.value) {
                this.$emit('generateStartSubTask', this.task.id, this.message);
            }
        },
        delayDelete() {
            setTimeout(() => this.$emit('deleteTask'), 500);
        },
        convertDate(date) {
            let year = String(date.getFullYear());
            let month = String(date.getMonth());
            month = month.length === 1 ? '0' + month : month;
            let day = String(date.getDate());
            day = day.length === 1 ? '0' + day : day;
            return year + month + day;
        },
        backSpace() {
            if (!this.task.value) {
                this.$emit('deleteTask');
            };
        }
    },
    watch: {
        isDone() {
            if (this.task.isDone) this.delayDelete();
        },
        listSubTasks() {
            if (this.task.subTasks.length === 0 && this.taskListId === 2) {
                this.$emit('generateStartSubTask', this.task.id);
            }
        }
    }
}

</script>

<template>

<div class="container-task"
    :class="{
        'failed': this.convertDate(task.executeDate) < this.convertDate(new Date()),
        'today' : this.convertDate(task.executeDate) === this.convertDate(new Date()),
        'normal': this.convertDate(task.executeDate) > this.convertDate(new Date())
    }"
    v-if="!task.isEdit && this.isShowRepeat">
    <input type="checkbox" :id="task.value"
    v-model="task.isDone">
    <div class="task-date-wrapper"
    @click="changeEditTask()">
        <p 
        :class="{ done: task.isDone,
                freezed: task.freezed}">{{ this.markedValue }}</p>
        <p v-if="this.task.executeDate" class="execute-date">
            {{ this.executeDate }}
        </p>
    </div>
    <p class="project-name" v-if="this.task.projectId">
        Проект: {{ this.task.projectName }}
    </p>
    <div class="container-type-task-icon"
    @click="$emit('changeEditTask')">
        <img v-if="this.task.typeTask === 'home'"
        src="/src/components/icons/homeTask.svg" 
        class="type-icon picked"
        :title="this.task.typeTask">
        <img v-if="this.task.typeTask === 'job'"
        src="/src/components/icons/jobTask.svg" 
        class="type-icon picked"
        :title="this.task.typeTask">
        <img v-if="this.task.typeTask === 'hobbie'"
        src="/src/components/icons/hobbieTask1.svg" 
        class="type-icon picked"
        :title="this.task.typeTask">
        <img v-if="this.task.isRepeat" 
        src="/src/components/icons/repeat.svg"
        class="type-icon picked">
        <img v-if="taskListId === 2"
        title="Добавить задачу"
        src="/src/components/icons/add.svg"
        class="type-icon control"
        @click.stop="this.$emit('createSubTask')"
        >
    </div>
    <img
    src="./icons/start-project.svg"
    title="Начать проект"
    @click="console.log(this.isShowRepeat)" 
    v-if="taskListId !== 2"
    class="type-icon control"
    >
    <img 
    src="./icons/show-list.svg"
    title="Показать подзадачи"
    class="type-icon"
    :class="{picked: this.task.isShowSubTasks,
            control: !this.task.isShowSubTasks}"
    v-if="taskListId === 2" 
    @click="this.$emit('changeVisibleSubTasks', task.id)"
    >
</div>
<div class="container-task" v-if="task.isEdit">
    <input type="checkbox" :id="task.value"
    v-model="task.isDone">
    <div class="task-date-wrapper-edit">
        <input type="text" :id="task.value + ' edit'"
        v-model="task.value"
        v-focus 
        @keyup.enter="changeEditTask(), this.$emit('addNewTask')"
        @keyup.esc="changeEditTask()"
        @keyup.delete="backSpace()">
        <input type="date" v-model="this.date"
        @change="changeEditTask">
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
    <img 
    src="./icons/save.svg"
    class="type-icon control"
    title="Сохранить"
    @click="changeEditTask">
</div>
</template>

<style>

input {
    margin: 3px;
    cursor: pointer;
    font-size: 1em;
}

div.task-date-wrapper-edit {
        display: flex;
        flex-direction: column;
        max-width: 20vw;
        flex-grow: 50;
}

div.task-date-wrapper-edit input {
    max-width: 40vw;
}

@media (min-width: 600px) {
    div.task-date-wrapper {
        display: flex;
        flex-direction: column;
        flex-grow: 50;
    }
    div.container-type-task-icon {
        flex-grow: 0;
        display: flex;
        align-items: center;
    }
    div.container-task {
        display: flex;
        flex-direction: row;
        align-items: center;
        width: calc(100% - 20px);
        min-height: 5vw;
        border-radius: 20px;
        margin: 10px;
        border: 1px solid black;
        flex-wrap:nowrap;
    }
    p {
        flex-grow: 1;
    }
}

@media (max-width: 600px) {
    div.task-date-wrapper {
        display: flex;
        flex-direction: column;
        flex: 50 2 auto;
        align-items:baseline;
        min-width: 20vw;
    }
    div.container-type-task-icon {
        display: flex;
        align-items: center;
        flex: 0 2 auto;
    }
    div.container-task {
        display: flex;
        flex-direction: row;
        align-items: center;
        width: calc(100% - 20px);
        min-height: 5vw;
        border-radius: 10px;
        margin: 10px;
        margin-top: 2px;
        margin-bottom: 2px;
        border: 1px solid black;
        flex-wrap: nowrap;
        overflow: auto;
    }
}

div.container-task {
    cursor: pointer;
}

div.normal {
    background-color: beige;
}

.failed {
    background-color: rgb(245, 217, 207);
}

.today {
    background-color: rgb(191, 248, 131);
}

p {
    padding: 5px 10px;
    max-width: 50vw;
    min-width: 20vw;
    overflow-wrap: break-word;
    flex: 5 2 auto;
}

p.execute-date {
    color: purple;
}

p.done {
    text-decoration: line-through;
}

p.freezed {
    background-color: rgb(89, 210, 237);
    border-radius: 10px;
}

p.project-name {
    color:blue;
    overflow-wrap: break-word;
    min-width: 20vw;
    flex: 1 2 auto;
}

img.type-icon {
    width: 30px;
    height: 30px;
    margin: 2px;
    padding: 5px;
    border-radius: 5px;
    border: 1px solid black;
    display: block;
    margin-right: 10px;
    cursor: pointer;
}

img.picked {
    background-color: greenyellow;
}

img.control {
    background-color: coral;
}

button {
    height: 30px;
    min-width: max-content;
    flex-wrap: nowrap;
    margin: 2px;
    border-radius: 10px;
    padding: 3px;
    cursor: pointer;
}

button.add-sub-task {
    display: block;
}


</style>