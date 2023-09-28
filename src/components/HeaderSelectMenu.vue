<script>
    export default {
        props: {
            taskLists: Array,
            selectedList: Object,
        },
        emits: [
            'changeSelected'
        ],
        mounted() {
            this.selected = this.selectedList.listName;
        },
        computed: {
            selected: {
                get() {
                    return this.selectedList.listName;
                },
                set(listName) {
                    this.$emit('changeSelected', listName);
                }
            }
        }
    }
</script>

<template>
    <div class="container-tools">
        <label for="menu">Выбрать список</label>
        <select name="menu" id="menu"
        v-model="selected">
            <option v-for="(list, index) in taskLists" 
            :value="list" 
            :key="index"
            :selected="selectedList === list"
            >{{ list }}</option>
            <option value="Создать список" key="-1" class="option-new-list">Создать список</option>
        </select>
    </div>
</template>

<style>

select {
    font-size: 20px;
    width:fit-content;
}

div.container-tools {
    display: flex;
    flex-direction: column;
    align-items: end;
}

label {
    font-size: 20px;
}

.option-new-list {
    color: rgb(255, 0, 0);
}

</style>