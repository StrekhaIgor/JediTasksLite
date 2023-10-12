<script>
export default {
    data() {
        return {
            pickedFilters: ['job', 'home', 'hobbie']
        }
    },
    emits: [
        'addNewTask',
        'filterTaskList'
    ],
    methods: {
        pickFilter(value) {
            if (this.pickedFilters.includes(value)) {
                this.pickedFilters = this.pickedFilters.filter((el) => el != value);
            } else {
                this.pickedFilters.push(value);
            };
            this.$emit('filterTaskList', this.pickedFilters);
        }
    }
}
</script>

<template>
<div class="tool-bar-wrapper">
    <button class="button-add-task"
     @click="$emit('addNewTask')">Добавить запись</button>
    <img 
    class="type-icon"
    :class="{picked: this.pickedFilters.includes('job')}" 
    src="./icons/jobTask.svg"
    @click="this.pickFilter('job')" 
    alt="job">
    <img 
    class="type-icon"
    :class="{picked: this.pickedFilters.includes('home')}"  
    src="./icons/homeTask.svg" 
    @click="this.pickFilter('home')"
    alt="home">
    <img 
    class="type-icon" 
    :class="{picked: this.pickedFilters.includes('hobbie')}" 
    src="./icons/hobbieTask1.svg" 
    @click="this.pickFilter('hobbie')"
    alt="hobbie">
</div>


</template>

<style>

.button-add-task {
    display: block;
    height: 30px;
    margin: 2px;
}

.tool-bar-wrapper {
    display: flex;
    flex-direction: row;
    align-items: center;
}

</style>