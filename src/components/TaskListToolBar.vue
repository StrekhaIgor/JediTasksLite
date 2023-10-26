<script>
export default {
    data() {
        return {
            pickedFilters: ['job', 'home', 'hobbie']
        }
    },
    props: {
        'selectedList': Object,
    },
    emits: [
        'addNewTask',
        'filterTaskList',
        'deleteTaskList'
    ],
    methods: {
        pickFilter(value) {
            if (this.pickedFilters.includes(value)) {
                this.pickedFilters = this.pickedFilters.filter((el) => el != value);
            } else {
                this.pickedFilters.push(value);
            };
            this.$emit('filterTaskList', this.pickedFilters);
        },
        clearStore() {
            localStorage.clear();
        }
    }
}
</script>

<template>
<div class="tool-bar-wrapper">
    <img
    src="./icons/add.svg"
    class="type-icon control"
    title="Добавить запись"
    @click="$emit('addNewTask')">
    <img 
    class="type-icon"
    :class="{picked: this.pickedFilters.includes('job')}" 
    src="./icons/jobTask.svg"
    title="По работе"
    @click="this.pickFilter('job')" 
    alt="job">
    <img 
    class="type-icon"
    :class="{picked: this.pickedFilters.includes('home')}"  
    src="./icons/homeTask.svg" 
    title="Домашние дела"
    @click="this.pickFilter('home')"
    alt="home">
    <img 
    class="type-icon" 
    :class="{picked: this.pickedFilters.includes('hobbie')}" 
    src="./icons/hobbieTask1.svg" 
    title="Хобби"
    @click="this.pickFilter('hobbie')"
    alt="hobbie">
    <img 
    v-if="this.selectedList.id > 2"
    src="./icons/delete.svg"
    class="type-icon control"
    title="Удалить список"
    @click="this.$emit('deleteTaskList')">
    <button @click="clearStore()">clearStore</button>

</div>


</template>

<style>

.tool-bar-wrapper {
    display: flex;
    flex-direction: row;
    align-items: center;
}

</style>