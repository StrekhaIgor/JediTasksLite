<script>
import HeaderApp from './components/HeaderApp.vue';
import TaskList from './components/TaskList.vue';

  export default {
    data() { 
      return {
        taskLists: [
        ],
        count: 100,
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
      jsonTaskLists() {
        return JSON.stringify(this.taskLists);
      }
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
        this.emitSubTask();
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
        let targetTasks = targetList.splice(targetList.findIndex((el) => el.id === taskId), 1);
        let projectId = targetTasks[0].projectId;
        if (projectId) {
          this.deleteSubTask(targetTasks[0].projectId, targetTasks[0].selfId);
          let targetTask = this.getTargetTask(2, projectId);
          if (!targetTask.subTasks.length) {
            this.generateStartSubTask(projectId, this._generateMessage(projectId));
          };
        };
        this.emitSubTask();
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
        this.deleteStartSubTask(projectId);
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
      createSubTask(projectId) {
        let targetProject = this.getTargetTask(2, projectId);
        targetProject.isShowSubTasks = true;
        let subTask = {
          isEdit: true,
          projectId: projectId,
        };
        subTask.id = Math.max(...targetProject.subTasks.map((elem) => elem.id)) + 1;
        targetProject.subTasks.push(subTask);
      },
      _generateMessage(projectId) {
        let targetProject = this.getTargetTask(2, projectId);
        return 'Cоздать задачу для проекта ' + targetProject.value;
      },
      generateStartSubTask(projectId, message) {
        let startTask = {};
        startTask.id = 0;
        startTask.projectId = projectId;
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
      deleteStartSubTask(projectId) {
        let targetProject = this.getTargetTask(2, projectId);
        targetProject.subTasks = targetProject.subTasks.filter((el) => el.id !== 0);
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
      },
      updateStore() {
        localStorage.setItem('appData', JSON.stringify(this.taskLists));
      },
      emitSubTask() {
        let projectList = this.taskLists[1].tasks;
        this.taskLists[0].tasks = this.taskLists[0].tasks
        .filter((task) => !task.projectId);
        let taskList = this.taskLists[0].tasks;
        for (let project of projectList) {
          let projectTasksInList = taskList
          .filter((task) => task.projectId === project.id);
          if (projectTasksInList.length) continue;
          for (let subTask of project.subTasks) {
            let newTask = {
              projectId: project.id,
              selfId: subTask.id,
              value: subTask.value,
              isEdit: false,
              isShow: true,
              subTasks: [],
              id: this.count++,
            };
            taskList.push(newTask);
            break;
          }
        }
      }
    },
    created() {
      let start = [
        {
          listName: 'Задачи',
          id: 1,
          isEdit: false,
          isSelect: false,
          tasks: [],
        },
        {
          listName: 'Проекты',
          id: 2,
          isEdit: false,
          isSelect: true,
          tasks: [],
        }
      ];
      let data = JSON.parse(localStorage.getItem('appData'));
      if (data) {
        this.taskLists = JSON.parse(localStorage.getItem('appData'));
      } else {
        this.taskLists = start;
      };
    },
    mounted() {
      this.sortTasks();
    },
    watch: {
      jsonTaskLists() {
        this.updateStore();
      }
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
  @repeat-task="repeatTask"
  @create-sub-task="createSubTask"/>
  <button @click="emitSubTask">test</button>
</template>

<style scoped>

</style>