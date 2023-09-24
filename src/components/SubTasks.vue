<script>
import SubTask from './SubTask.vue';
export default {
    props: {
        subTasks: Array,
        projectId: Number,
    },
    components: {
        SubTask,
    },
    emits: [
        'changeEditSubTask',
    ]
}
</script>

<template>
    <div class="subtask">
        <sub-task v-for="subtask in subTasks"
        :subTask="subtask"
        @change-edit-sub-task="this.$emit('changeEditSubTask', this.projectId, subtask.id)" />
    </div>
</template>

<style>

div.subtask {
    padding-left: 3vw;
}

</style>