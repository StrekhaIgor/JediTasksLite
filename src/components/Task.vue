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
        'createSubTask',
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
            if (!this.task.isRepeat) return true;
            if (this.task.executeDate.getDate() === new Date().getDate()) return true;
            return false;
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
        },
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

<div class="container-task" v-if="!task.isEdit && this.isShowRepeat">
    <input type="checkbox" :id="task.id"
    v-model="task.isDone">
    <div class="task-date-wrapper"
    @click="$emit('changeEditTask')">
        <p 
        :class="{ done: task.isDone}">{{ task.value }}</p>
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
    @click="$emit('moveTaskToProjects')" 
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
        <input type="text" :id="task.id"
        v-model="task.value" 
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
        background-color: beige;
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
        flex: 0, 2, auto;
    }
    div.container-task {
        display: flex;
        flex-direction: row;
        align-items: center;
        width: calc(100% - 20px);
        min-height: 5vw;
        background-color: beige;
        border-radius: 10px;
        margin: 10px;
        margin-top: 2px;
        margin-bottom: 2px;
        border: 1px solid black;
        flex-wrap: nowrap;
        overflow: auto;
    }
}

p {
    padding: 5px 10px;
    max-width: 50vw;
    min-width: 20vw;
    overflow-wrap: break-word;
    flex: 5, 2, auto;
}

p.execute-date {
    color: purple;
}

p.done {
    text-decoration: line-through;
}

p.project-name {
    color:blue;
    overflow-wrap: break-word;
    min-width: 20vw;
    flex: 1, 2, auto;
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
}

button.add-sub-task {
    display: block;
}


</style>