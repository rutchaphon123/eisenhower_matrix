// src/App.vue
<template>
  <div id="app">
    <div class="matrix">
      <div v-for="(quadrant, index) in quadrants" :key="index" class="quadrant">
        <h2>{{ quadrant.title }}</h2>
        <draggable v-model="quadrant.tasks" group="tasks" @change="saveToLocalStorage">
          <div v-for="task in quadrant.tasks" :key="task.id" class="task">
            {{ task.text }}
            <button @click="editTask(task)">Edit</button>
            <button @click="deleteTask(task)">Delete</button>
          </div>
        </draggable>
        <button @click="addTask(index)">Add Task</button>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'

export default {
  name: 'App',
  components: {
    draggable
  },
  data() {
    return {
      quadrants: [
        { title: 'Urgent & Important', tasks: [] },
        { title: 'Important & Not Urgent', tasks: [] },
        { title: 'Urgent & Not Important', tasks: [] },
        { title: 'Not Urgent & Not Important', tasks: [] }
      ]
    }
  },
  mounted() {
    this.loadFromLocalStorage()
  },
  methods: {
    addTask(quadrantIndex) {
      const taskText = prompt('Enter task:')
      if (taskText) {
        this.quadrants[quadrantIndex].tasks.push({
          id: Date.now(),
          text: taskText
        })
        this.saveToLocalStorage()
      }
    },
    editTask(task) {
      const newText = prompt('Edit task:', task.text)
      if (newText) {
        task.text = newText
        this.saveToLocalStorage()
      }
    },
    deleteTask(task) {
      for (let quadrant of this.quadrants) {
        const index = quadrant.tasks.findIndex(t => t.id === task.id)
        if (index !== -1) {
          quadrant.tasks.splice(index, 1)
          this.saveToLocalStorage()
          break
        }
      }
    },
    saveToLocalStorage() {
      localStorage.setItem('eisenhowerMatrix', JSON.stringify(this.quadrants))
    },
    loadFromLocalStorage() {
      const saved = localStorage.getItem('eisenhowerMatrix')
      if (saved) {
        this.quadrants = JSON.parse(saved)
      }
    }
  }
}
</script>

<style>
.matrix {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-gap: 10px;
}
.quadrant {
  border: 1px solid #ccc;
  padding: 10px;
}
.task {
  margin: 5px 0;
  padding: 5px;
  background-color: #f0f0f0;
  cursor: pointer;
}
</style>