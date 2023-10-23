<script>
import SubTask from './SubTask.vue';
export default {
    props: {
        project: Object,
    },
    components: {
        SubTask,
    },
    computed: {
        message() {
            return 'Cоздать задачу для проекта ' + this.project.value;
        }
    },
    emits: [
        'changeEditSubTask',
        'deleteSubTask',
        'createSubTask'
    ],
}
</script>

<template>
    <div class="subtask">
        <sub-task v-for="subtask in project.subTasks"
        :sub-task="subtask"
        :key="subtask.id"
        @change-edit-sub-task="this.$emit('changeEditSubTask', this.project.id, subtask.id)"
        @delete-sub-task="this.$emit('deleteSubTask', this.project.id, subtask.id)"
        @create-sub-task="$emit('createSubTask')" />
    </div>
</template>

<style>

div.subtask {
    padding-left: 3vw;
}

</style>