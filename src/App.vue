<template>
  <div id="app">
    <h1>Tarefas</h1>
    <TaskProgress :progress="progress" />
    <NewTask @taskAdded="addTask" />
    <TaskGrid
      :tasks="tasks"
      @taskStateChange="toggleTaskState"
      @taskDeleted="deleteTask"
    />
  </div>
</template>

<script>
import TaskGrid from './components/TaskGrid'
import NewTask from './components/NewTask'
import TaskProgress from './components/TaskProgress'

export default {
  name: 'App',

  components: {
    TaskGrid, NewTask, TaskProgress
  },

  data: () => ({
    tasks: []
  }),

  created () {
    const json = localStorage.getItem('tasks')
    this.tasks = JSON.parse(json) || []
  },

  computed: {
    progress () {
      const total = this.tasks.length
      const done = this.tasks.filter(t => !t.pending).length
      return Math.round(done / total * 100) || 0
    }
  },

  watch: {
    tasks: {
      deep: true,
      handler () {
        localStorage.setItem('tasks', JSON.stringify(this.tasks))
      }
    }
  },

  methods: {
    addTask (task) {
      const sameName = t => t.name === task.name
      const reallyNew = this.tasks.filter(sameName).length === 0
      if (reallyNew) {
        this.tasks.push({
          name: task.name,
          pending: task.pending || true
        })
      }
    },
    deleteTask (i) {
      this.tasks.splice(i, 1)
    },
    toggleTaskState (i) {
      this.tasks[i].pending = !this.tasks[i].pending
    }
  }
}
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Yellowtail");

body {
  font-family: "Lato", sans-serif;
  background: linear-gradient(to top, rgb(173, 83, 137), rgb(60, 16, 83));
  color: #fff;
}

#app {
  display: flex;
  flex: 1;
  flex-direction: column;

  align-items: center;
  height: 100vh;
}

#app h1 {
  margin-bottom: 5px;
  font-family: "Yellowtail";
  font-size: 150px;
  font-weight: 300;
  color: white;
}
</style>
