<script>

const focus = {
    mounted(el) {
        el.focus();
    }
}
export default {
    data() {
        return {
            countBackSpace: 0,
        }
    },
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
                    return new Date().toISOString().split('T')[0];
                }
            },
            set(executeDate) {
                this.$emit('setExecuteDate', this.taskListId, this.task.id, executeDate);
            }
        },
        isDone() {
            return this.task.isDone;
        },
        executeDate() {
            if (this.task.executeDate === new Date().toISOString().split('T')[0]) {
                return 'Сегодня';
            } else return this.convertDate(this.task.executeDate);
        },
        listSubTasks() {
            return JSON.stringify(this.task.subTasks);
        },
        markedValue() {
            if (this.task.freezed) {
                return this.task.value + ' [Остановлен]';
            };
            return this.task.value;
        },
        importantTask() {
            this.task.importantTask = this.task.value.includes('!!!');
            if (this.task.projectName && this.task.projectName.includes('!!!')) {
                this.task.importantTask = true;
            } 
            return this.task.importantTask;
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
            return date.split('-').reverse().join('.');
        },
        backSpace() {
            if (!this.task.value) {
                if (this.countBackSpace) {
                    this.$emit('deleteTask');
                    this.countBackSpace = 0;
                } else {
                    this.countBackSpace++;
                }
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
        'failed': this.task.executeDate < new Date().toISOString().split('T')[0],
        'today' : this.task.executeDate === new Date().toISOString().split('T')[0],
        'normal': this.task.executeDate > new Date().toISOString().split('T')[0]
    }"
    v-if="!task.isEdit">
    <input type="checkbox" :id="task.value"
    v-model="task.isDone">
    <div class="task-date-wrapper"
    @click="changeEditTask()">
        <p class="task-value"
        :class="{ done: task.isDone,
                freezed: task.freezed,
                imp: this.importantTask}">{{ this.markedValue }}</p>
        <p v-if="this.task.executeDate && !this.importantTask" class="execute-date">
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
    @click="this.$emit('moveTaskToProjects')" 
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
    <input type="checkbox" :id="task.id"
    v-model="task.isDone">
    <div class="task-date-wrapper-edit">
        <textarea type="text" :id="task.value + ' edit'"
        v-model="task.value"
        v-focus 
        @keyup.enter="changeEditTask(), this.$emit('addNewTask')"
        @keyup.esc="changeEditTask()"
        @keyup.delete="backSpace()"></textarea>
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

textarea {
    margin: 3px;
    cursor: pointer;
    font-size: 1em;
    padding: 3px;
}


@media (min-width: 600px) {
    * {
        font-size: 18px;
    }

    div.task-date-wrapper-edit {
        display: flex;
        flex-direction: column;
        min-width: 0px;
        flex: 1 1 auto;
    }

    div.task-date-wrapper-edit input {
        max-width: 40vw;
    }

    div.task-date-wrapper-edit textarea {
        max-width: 40vw;
    }

    div.task-date-wrapper {
        display: flex;
        flex-direction: column;
        flex: 2 1 auto;
        margin-right: 10px;
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
        border-radius: 15px;
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
        flex: 1 1 auto;
        min-width: 0px;
        align-items:baseline;
        overflow:visible;
        margin-right: 10px;
    }

    div.task-date-wrapper-edit {
        display: flex;
        flex-direction: column;
        flex-grow: 50;
        min-width: 0px;
    }

    div.container-type-task-icon {
        display: flex;
        align-items: center;
        flex: 0 0 auto;
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
        overflow :visible;
    }
    p {
        flex: 1 1 auto;
        overflow-wrap: break-word;
    }
    p.imp {
        width: 100%;
    }

    p.task-value {
        width: 100%;
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
    overflow-wrap: break-word;
    flex: 1 1 auto;
}

p.execute-date {
    color: purple;
    flex: 0 1 auto;
}

p.done {
    text-decoration: line-through;
}

p.freezed {
    background-color: rgb(89, 210, 237);
    border-radius: 10px;
}

p.imp {
    background-color: red;
    border-radius: 10px;
    min-width: 0px;
    overflow: hidden;
    overflow-wrap: break-word;
    margin: 5px;
}

p.project-name {
    color:blue;
    overflow: hidden;
    overflow-wrap: break-word;
    flex: 1 1 auto;
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


</style>