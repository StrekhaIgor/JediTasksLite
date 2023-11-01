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
        'deleteSubTask',
        'generateStartSubTask',
        'setTypeTask',
        'repeatTask',
        'createSubTask',
        'filterTaskList',
        'deleteTaskList'
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
        deleteSubTask(projectId, subTaskId) {
            this.$emit('deleteSubTask', projectId, subTaskId);
        },
        generateStartSubTask(projectId) {
            this.$emit('generateStartSubTask', projectId);
        },
        setTypeTask(taskListId, taskId, value) {
            this.$emit('setTypeTask', taskListId, taskId, value);
        },
        filterTaskList(arrayOfFilters) {
            this.$emit('filterTaskList', arrayOfFilters);
        }
    }
}

</script>

<template>

<div class="container-tasks">
    <task-list-tool-bar v-if="!selectedList.isEdit"
    :selected-list="this.selectedList"
    @add-new-task="$emit('addNewTask')"
    @filterTaskList="filterTaskList"
    @delete-task-list="this.$emit('deleteTaskList', this.selectedList.id)"/>
    <input type="text" v-if="selectedList.isEdit" v-model="this.listName"
    @blur="createNewList"
    @keyup.enter="createNewList"
    @click="clearInputName">
    <template v-for="task in selectedList.tasks">
        <task v-if="task.isShow"
        :task="task"
        :task-list-id="selectedList.id"
        @delete-task="$emit('deleteTask', selectedList.id, task.id)"
        @change-edit-task="$emit('changeEditTask', selectedList.id, task.id)"
        @move-task-to-projects="$emit('moveTaskToProjects', selectedList.id, task.id)"
        @set-execute-date="setExecuteDate"
        @change-visible-sub-tasks="changeVisibleSubTasks"
        @generate-start-sub-task="generateStartSubTask"
        @set-type-task="setTypeTask"
        @repeat-task="$emit('repeatTask', task.id)"
        @create-sub-task="$emit('createSubTask', task.id)"
        @add-new-task="$emit('addNewTask')"/>
        <template v-if="selectedList.id === 2 && task.isShowSubTasks">
            <sub-tasks
            v-if="task.isShow"
            :project="task"
            @change-edit-sub-task="changeEditSubTask"
            @delete-sub-task="deleteSubTask"
            @create-sub-task="$emit('createSubTask', task.id)"/>
        </template>
    </template>
</div>

</template>

<style>

</style>