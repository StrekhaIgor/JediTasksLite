<script>
export default {
    emits: [
        'addNewTask'
    ]
}
</script>

<template>
<div>
    <button @click="$emit('addNewTask')">Добавить запись</button>
</div>


</template>

<style>

</style>