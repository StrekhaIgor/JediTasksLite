<script>
import Task from './Task.vue';
import TaskListToolBar from './TaskListToolBar.vue';
import SubTasks from './SubTasks.vue';

export default {
    components: {
        Task,
        TaskListToolBar,
        SubTasks,
    },
    data() {
        return {
            listName: 'Введите имя списка',
        }
    },
    props: {
        'selectedList': Object,
    },
    emits: [
        'deleteTask', 
        'changeEditTask', 
        'addNewTask', 
        'moveTaskToProjects', 
        'changeEditListName',
        'changeSelected',
        'setExecuteDate',
        'changeEditSubTask',
        'changeVisibleSubTasks',
        'createStartTaskOfProject'
    ],
    methods: {
        createNewList() {
            this.$emit('changeEditListName', this.selectedList.id, this.listName);
            this.$emit('changeSelected', this.listName);
            this.listName = 'Ввeдите имя списка';

        },
        clearInputName() {
            this.listName = '';
        },
        setExecuteDate(listId, taskId, executeDate) {
            this.$emit('setExecuteDate', listId, taskId, executeDate);
        },
        changeEditSubTask(projectId, subTaskId) {
            this.$emit('changeEditSubTask', projectId, subTaskId);
        },
        changeVisibleSubTasks(projectId) {
            this.$emit('changeVisibleSubTasks', projectId);
        },
        createStartTaskOfProject(task) {
            this.$emit('createStartTaskOfProject', task)
        }
    }
}

</script>

<template>

<div class="container-tasks">
    <task-list-tool-bar v-if="!selectedList.isEdit"
    @addNewTask="$emit('addNewTask')"/>
    <input type="text" v-if="selectedList.isEdit" v-model="this.listName"
    @blur="createNewList"
    @keyup.enter="createNewList"
    @click="clearInputName">
    <template v-for="task in selectedList.tasks">
        <task :task="task"
        :task-list-id="selectedList.id"
        @deleteTask="$emit('deleteTask', selectedList.id, task.id)"
        @changeEditTask="$emit('changeEditTask', selectedList.id, task.id)"
        @moveTaskToProjects="$emit('moveTaskToProjects', selectedList.id, task.id)"
        @set-execute-date="setExecuteDate"
        @change-visible-sub-tasks="changeVisibleSubTasks"
        @createStartTaskOfProject="createStartTaskOfProject"/>
        <template v-if="selectedList.id === 2 && task.isShowSubTasks">
            <sub-tasks
            :sub-tasks="task.subTasks"
            :project-id="task.id"
            @change-edit-sub-task="changeEditSubTask"/>
        </template>
    </template>
</div>

</template>

<style>

</style>