<script>
import HeaderApp from './components/HeaderApp.vue';
import TaskList from './components/TaskList.vue';

  export default {
    data() { 
      return {
        taskLists: [
          {
            listName: 'Задачи',
            id: 1,
            isSelect: false,
            isEdit: false,
            tasks: [
              {
                value: 'сходить за хлебом',
                id: 1,
                isDone: false,
                isEdit: false,
                executeDate: new Date(2023, 11, 1),
                typeTask: 'home'
              },
              {
                value: 'написать приложуху',
                id: 2,
                isDone: false,
                isEdit: false,
                executeDate: new Date(2023, 10, 1),
                typeTask: 'hobbie'
              }
            ]
          },
          {
            listName: 'Проекты',
            id: 2,
            isSelect: true,
            isEdit: false,
            tasks: [
              {
                value: 'выучить Vue3',
                id: 1,
                isDone: false,
                isEdit: false,
                isShowSubTasks: true,
                subTasks: [
                  {
                    id: 1,
                    value: '123',
                    isDone: false,
                    isEdit: false,
                    date: new Date,
                  }
                ]
              },
              {
                value: 'Выучить PHP',
                id: 2,
                isDone: false,
                isEdit: false,
                isShowSubTasks: true,
                subTasks: [],
              }
            ]
          }
        ],
        count: 'one',
      }
    },
    computed: {
      arrayOfLists() {
        return this.taskLists.map((el) => el.listName);
      },
      selectedList() {
        return this.taskLists
        .filter((el) => el.isSelect)[0];
      },
    },
    components: {
      HeaderApp,
      TaskList,
    },
    methods: {
      changeSelected(listName) {
        if (listName === 'Создать список') {
          this.createNewList();
        };
        for (let list of this.taskLists) {
          list.isSelect = (list.listName === listName);
        };
      },
      getTargetList(listId) {
        return this.taskLists
        [this.taskLists.findIndex((el) => el.id === listId)];
      },
      getTargetTask(listId, taskId) {
        for (let task of this.getTargetList(listId).tasks) {
          if (task.id === taskId) return task;
        }
      },
      refreshTaskId() {
        for (let index = 0; index < this.selectedList.tasks.length; index++) {
          this.selectedList.tasks[index].id = index + 1;
        }
      },
      deleteTask(listId, taskId) {
        let targetList = this.getTargetList(listId)
        .tasks;
        targetList.splice(targetList.findIndex((el) => el.id === taskId), 1);
        this.refreshTaskId();
      },
      changeEditTask(listId, taskId) {
        let targetTask = this.getTargetTask(listId, taskId);
        targetTask.isEdit = !targetTask.isEdit;
        this.sortTasks();
      },
      addNewTask() {
        let newTask = {};
        newTask.isEdit = true;
        newTask.subTasks = [];
        this.selectedList.tasks.unshift(newTask);
        this.refreshTaskId();
        this.sortTasks();
      },
      moveTaskToProjects(taskListId, taskId) {
        let taskList = this.taskLists
        .filter((list) => list.id === taskListId)[0]
        .tasks;
        let index = 0;
        for (let i = 0; i < taskList.length; i++) {
          if (taskList[i].id === taskId) {
            index = i;
            break;
          };
        }
        let movedTask = taskList.splice(index, 1)[0];
        movedTask.id = this.taskLists[1].tasks.length + 1;
        this.taskLists[1].tasks.push(movedTask);
      },
      createNewList() {
        let newList = {};
        newList.isEdit = true;
        newList.isSelect = true;
        newList.id = this.taskLists.length + 1;
        newList.listName = 'Создать список';
        newList.tasks = [];
        this.taskLists.push(newList);
      },
      changeEditListName(listID, listName) {
        for (let list of this.taskLists) {
          if (list.id === listID) {
            list.isEdit = !list.isEdit;
            list.listName = listName;
          }
        }
      },
      setExecuteDate(listId, taskId, executeDate) {
        this.getTargetTask(listId, taskId)
        .executeDate = executeDate;
      },
      changeEditSubTask(projectId, subTaskId) {
        let targetList = this.taskLists[1];
        let targetProject = targetList.tasks
        .filter((project) => project.id === projectId)[0];
        let targetSubTask = targetProject.subTasks
        .filter((subTask) => subTask.id === subTaskId)[0];
        targetSubTask.isEdit = !targetSubTask.isEdit;
      },
      changeVisibleSubTasks(projectId) {
        let projectList = this.taskLists[1].tasks;
        let targetProject = projectList
        .filter((project) => project.id === projectId)[0];
        targetProject.isShowSubTasks = !targetProject.isShowSubTasks;
      },
      deleteSubTask(projectId, subTaskId) {
        let targetProject = this
        .taskLists[1]
        .tasks
        .filter((project) => project.id === projectId)[0];
        targetProject.subTasks = targetProject.subTasks
        .filter((subTask) => subTask.id !== subTaskId);
      },
      generateStartSubTask(projectId, message) {
        let startTask = {};
        startTask.id = 0;
        startTask.value = message;
        startTask.isDone = false;
        startTask.isEdit = false;
        let targetSubTasks =
          this.taskLists[1]
          .tasks
          .filter(project => project.id === projectId)[0];
        targetSubTasks.subTasks = targetSubTasks.subTasks.filter(subTask => subTask.id !== 0);
        targetSubTasks.subTasks.unshift(startTask);
      },
      sortTasks() {
        this.taskLists[0].tasks.sort((a, b) => a.executeDate - b.executeDate);
      },
      setTypeTask(taskListId, taskId, value) {
        let targetTask = this.getTargetTask(taskListId, taskId);
        targetTask.typeTask = value;
      },
      repeatTask(taskId) {
        let targetTask = this.getTargetTask(1, taskId);
        targetTask.isRepeat = !targetTask.isRepeat;
      }
    },
    mounted() {
      this.sortTasks();
    }
  }
</script>

<template>
  <HeaderApp 
  :task-lists="this.arrayOfLists"
  :selected-list="this.selectedList"
  @change-selected="changeSelected"/>
  <task-list :selected-list="this.selectedList"
  @delete-task="deleteTask"
  @change-edit-task="changeEditTask"
  @add-new-task="addNewTask"
  @move-task-to-projects="moveTaskToProjects"
  @change-edit-list-name="changeEditListName"
  @change-selected="changeSelected"
  @set-execute-date="setExecuteDate"
  @change-edit-sub-task="changeEditSubTask"
  @change-visible-sub-tasks="changeVisibleSubTasks"
  @delete-sub-task="deleteSubTask"
  @generateStartSubTask="generateStartSubTask"
  @set-type-task="setTypeTask"
  @repeat-task="repeatTask" />
  <button @click="sortTasks">test</button>
</template>

<style scoped>

</style>