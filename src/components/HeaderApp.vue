<script>
import HeaderSelectMenu from './HeaderSelectMenu.vue';

export default {
    data() {
        return {
            name: 'HeaderApp',
            coord: {},
        }
    },
    computed: {
        styleList() {
            return {
                position: 'absolute',
                top: this.coord.bottom + 50  + 'px',
                color: 'red',
            }
        }
    },
    methods: {
        getCoord(elem) {
            console.log(elem.getBoundingClientRect());
            console.log(elem);
        },
        changeSelected(listName) {
            this.$emit('changeSelected', listName);
        }
    },
    components: { HeaderSelectMenu },
    props: {
        taskLists: Array,
        selectedList: Object,
    },
    emits: [
        'changeSelected'
    ],
}
</script>

<template>
    <div class="header">
        <div>
            <img src="./icons/mainIcon.png" alt="Icon" class="main-icon">
        </div>
        <h1>
            Jedi Tasks<span>Lite</span>
        </h1>
        <header-select-menu 
        :taskLists="taskLists"
        :selectedList="selectedList"
        @changeSelected="changeSelected"/>
    </div>
</template>

<style>
    img.main-icon {
        width: 10vh;
        height: 10vh;
        min-width: 50px;
        min-height: 50px;
        display: inline-block;
        border-radius: 10px;
    }

    h1 {
        display: inline-block;
        font-size: 7vh;
        flex-grow: 1;
        margin: 5px;
        font-family: 'Concert One', cursive;
        color: rgb(65, 184, 131); /* Vue green color */
    }

    h1 span {
        font-size: 5vh;
        padding: 5px;
        color: rgb(53, 73, 94); /* Vue gray color */
    }

    div.header {
        padding: 10px;
        display: flex;
        align-items: center;
        border-radius: 2vh;
    }

    div.menu {
        position: absolute;
        text-align: end;
        width: max-content;
        
    }
</style>