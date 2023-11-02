<script>
import HeaderApp from './components/HeaderApp.vue';
import TaskList from './components/TaskList.vue';

  export default {
    data() { 
      return {
        taskLists: [
        ],
        today: new Date().toDateString(),
        countTasks: 1,
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
          if (task.id === taskId) {
            return task;
          };
        }
      },
      deleteTask(listId, taskId) {
        let targetList = this.getTargetList(listId)
        let targetTask = targetList
        .tasks
        .splice(targetList.tasks
        .findIndex((el) => el.id === taskId), 1)[0];
        let projectId = targetTask.projectId;
        if (projectId && !targetTask.isRepeat) {
          this.deleteSubTask(targetTask.projectId, targetTask.selfId);
          let targetProject = this.getTargetTask(2, projectId);
          if (!targetProject.subTasks.length) {
            this.generateStartSubTask(projectId, this._generateMessage(projectId));
          };
          targetProject.freezed = false;
          this.emitSubTask();
        };
        
        if (targetTask.isRepeat) {
          targetTask.isDone = false;
          targetTask.executeDate.setDate(targetTask.executeDate.getDate() + 2);
          this.taskLists[0].tasks.push(targetTask);
        };
        this.sortTasks();
      },
      changeEditTask(listId, taskId) {
        let targetTask = this.getTargetTask(listId, taskId);
        if (!targetTask.value) {
          this.deleteTask(listId, taskId);
          return;
        }
        targetTask.isEdit = !targetTask.isEdit;
        this.sortTasks();
      },
      addNewTask() {
        let newTask = {};
        newTask.isEdit = true;
        newTask.isShow = true;
        newTask.subTasks = [];
        newTask.isShowSubTasks = true;
        newTask.executeDate = new Date(this.today);
        newTask.id = this.countTasks++;
        this.selectedList.tasks.unshift(newTask);
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
        if (movedTask.projectId) {
          this.deleteSubTask(movedTask.projectId, movedTask.selfId);
          let freezedProject = this.getTargetTask(2, movedTask.projectId);
          freezedProject.freezed = true;
          this.emitSubTask();
        };
        movedTask.isShowSubTasks = true;
        this.taskLists[1].tasks.push(movedTask);
        this.generateStartSubTask(taskId);
        this.emitSubTask();
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
        if (!targetSubTask.value) {
          this.deleteSubTask(projectId, subTaskId);
        };
        if (targetSubTask.id === 0) {
          this.deleteStartSubTask(projectId);
          this.createSubTask(projectId);
        } else { 
          targetSubTask.isEdit = !targetSubTask.isEdit;
        }
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
          typeTask: targetProject.typeTask,
        };
        subTask.id = this.countTasks++;
        this.deleteStartSubTask(projectId);
        targetProject.subTasks.push(subTask);
      },
      _generateMessage(projectId) {
        let targetProject = this.getTargetTask(2, projectId);
        return 'Cоздать задачу для проекта ' + targetProject.value;
      },
      generateStartSubTask(projectId) {
        let startTask = {};
        startTask.id = 0;
        startTask.projectId = projectId;
        startTask.value = 'Создать задачу';
        startTask.isDone = false;
        startTask.isEdit = false;
        let targetSubTasks =
          this.taskLists[1]
          .tasks
          .filter(project => project.id === projectId)[0];
        if (targetSubTasks.subTasks.length) return;
        startTask.typeTask = targetSubTasks.typeTask;
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
        .filter((task) => !task.projectId || task.isRepeat);
        let taskList = this.taskLists[0].tasks;
        for (let project of projectList) {
          let projectTasksInList = taskList
          .filter((task) => task.projectId === project.id);
          if (projectTasksInList.length || project.freezed) continue;
          for (let subTask of project.subTasks) {
            let newTask = {
              projectId: project.id,
              projectName: project.value,
              selfId: subTask.id,
              value: subTask.value,
              isEdit: false,
              isShow: true,
              subTasks: [],
              typeTask: project.typeTask,
              id: this.countTasks++,
              executeDate: new Date(this.today),
            };
            taskList.push(newTask);
            break;
          }
        };
        this.sortTasks();
      },
      filterTaskList(arrayOfFilters) {
        for (let taskList of this.taskLists) { 
          for (let task of taskList.tasks) {
            if (!task.typeTask) continue;
            if (task.typeTask && arrayOfFilters.includes(task.typeTask)) {
              task.isShow = true;
            } else {
              task.isShow = false;
            }
          }
        }
      },
      deleteTaskList(taskListId) {
        if (taskListId === 1 || taskListId === 2) return;
        this.taskLists = this.taskLists.filter((list) => list.id !== taskListId);
        this.changeSelected('Задачи');
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
        for (let taskList of this.taskLists) {
          for (let task of taskList.tasks) {
            task.executeDate = new Date(task.executeDate);
            if (task.isRepeat && task.executeDate < new Date(this.today)) {
              task.executeDate = new Date(this.today);
            }
          }
        }
      } else {
        this.taskLists = start;
      };
    },
    mounted() {
      this.sortTasks();
      this.today = new Date().toDateString();
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
  @create-sub-task="createSubTask"
  @filterTaskList="filterTaskList"
  @delete-task-list="deleteTaskList"
  @go-to-projects="changeSelected('Проекты')"/>
</template>

<style scoped>

</style>