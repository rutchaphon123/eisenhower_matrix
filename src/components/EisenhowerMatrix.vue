<template>
  <div id="app">
    <div class="matrix">
      <div v-for="(quadrant, index) in quadrants" :key="index" :class="['quadrant', `quadrant-${index + 1}`]">
        <h2>{{ quadrant.title }}</h2>
        <draggable v-model="quadrant.tasks" group="tasks" @change="saveToLocalStorage" class="drop-zone">
          <div v-for="task in quadrant.tasks" :key="task.id" class="task">
            {{ task.text }}
            <button @click="editTask(task)" class="edit-task">Edit</button>
            <button @click="deleteTask(task)" class="delete-task">Delete</button>
          </div>
          <div v-if="quadrant.tasks.length === 0" class="empty-message">
            Drop tasks here
          </div>
        </draggable>
        <button @click="addTask(index)" class="add-task">Add Task</button>
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
  border-radius: 8px;
}

.quadrant-1 {
  background-color: #ffcccb; /* Light red for Urgent & Important */
}

.quadrant-2 {
  background-color: #ffffcc; /* Light yellow for Important & Not Urgent */
}

.quadrant-3 {
  background-color: #e6f3ff; /* Light blue for Urgent & Not Important */
}

.quadrant-4 {
  background-color: #e6ffe6; /* Light green for Not Urgent & Not Important */
}

.drop-zone {
  min-height: 100px;
  border: 2px dashed #999;
  border-radius: 4px;
  padding: 10px;
  margin-bottom: 10px;
  background-color: rgba(255, 255, 255, 0.7);
}

.task {
  margin: 5px 0;
  padding: 10px;
  background-color: #ffffff;
  cursor: move;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.empty-message {
  color: #666;
  text-align: center;
  padding: 20px;
}

.add-task {
  color: #ffffff;
  background-color: rgb(217, 151, 19);
  border: none;
  border-radius: 30px;
  padding: 15px 32px;
  cursor: pointer;
}

.add-task:hover {
  background-color: rgba(196, 154, 16, 0.489);
}

.edit-task {
  color: #ffffff;
  background-color: rgb(29, 196, 43);
  border: none;
  border-radius: 30px;
  padding: 7px 16px;
  margin-left: 5px;
  cursor: pointer;
}

.delete-task {
  color: #ffffff;
  background-color: rgb(206, 33, 14);
  border: none;
  border-radius: 30px;
  padding: 7px 16px;
  margin-left: 5px;
  cursor: pointer;
}
</style>