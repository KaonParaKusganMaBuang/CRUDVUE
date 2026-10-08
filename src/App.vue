<script setup>
import { ref, computed } from 'vue'

const tasks = ref([
  {
    id: 1,
    title: 'Create Vue Project',
    subject: 'Web Development',
    description: 'Create a CRUD application using Vue.',
    dueDate: '2026-10-15',
    priority: 'High',
    status: 'Pending'
  },
  {
    id: 2,
    title: 'Database Activity',
    subject: 'Database',
    description: 'Complete the MySQL CRUD activity.',
    dueDate: '2026-10-18',
    priority: 'Medium',
    status: 'In Progress'
  }
])

const form = ref({
  title: '',
  subject: '',
  description: '',
  dueDate: '',
  priority: 'Medium',
  status: 'Pending'
})

const editingId = ref(null)
const search = ref('')

const filteredTasks = computed(() => {
  return tasks.value.filter(task =>
    task.title.toLowerCase().includes(search.value.toLowerCase()) ||
    task.subject.toLowerCase().includes(search.value.toLowerCase()) ||
    task.status.toLowerCase().includes(search.value.toLowerCase())
  )
})

function submitTask() {
  if (
    !form.value.title ||
    !form.value.subject ||
    !form.value.description ||
    !form.value.dueDate
  ) {
    alert('Please complete all required fields.')
    return
  }

  if (editingId.value) {
    const index = tasks.value.findIndex(
      task => task.id === editingId.value
    )

    if (index !== -1) {
      tasks.value[index] = {
        id: editingId.value,
        ...form.value
      }
    }

    alert('Task updated successfully!')
  } else {
    tasks.value.push({
      id: Date.now(),
      ...form.value
    })

    alert('Task added successfully!')
  }

  resetForm()
}

function editTask(task) {
  editingId.value = task.id

  form.value = {
    title: task.title,
    subject: task.subject,
    description: task.description,
    dueDate: task.dueDate,
    priority: task.priority,
    status: task.status
  }

  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  })
}

function deleteTask(id) {
  const confirmDelete = confirm(
    'Are you sure you want to delete this task?'
  )

  if (confirmDelete) {
    tasks.value = tasks.value.filter(task => task.id !== id)
  }
}

function resetForm() {
  editingId.value = null

  form.value = {
    title: '',
    subject: '',
    description: '',
    dueDate: '',
    priority: 'Medium',
    status: 'Pending'
  }
}

function getPriorityClass(priority) {
  return priority.toLowerCase()
}

function getStatusClass(status) {
  return status.toLowerCase().replace(' ', '-')
}
</script>

<template>
  <div class="app">

    <header class="header">
      <div>
        <h1>Student Task Manager</h1>
        <p>Manage your school tasks easily</p>
      </div>

      <div class="total">
        <strong>{{ tasks.length }}</strong>
        <span>Total Tasks</span>
      </div>
    </header>

    <main class="container">

      <section class="card form-card">
        <div class="card-header">
          <div>
            <h2>
              {{ editingId ? ' Edit Task' : ' Add New Task' }}
            </h2>

            <p>
              {{
                editingId
                  ? 'Update the information below.'
                  : 'Enter your task information below.'
              }}
            </p>
          </div>
        </div>

        <form @submit.prevent="submitTask">

          <div class="form-grid">

            <div class="form-group">
              <label>Task Name *</label>

              <input
                v-model="form.title"
                type="text"
                placeholder="Enter task name"
              />
            </div>

            <div class="form-group">
              <label>Subject *</label>

              <input
                v-model="form.subject"
                type="text"
                placeholder="e.g. Web Development"
              />
            </div>

            <div class="form-group">
              <label>Due Date *</label>

              <input
                v-model="form.dueDate"
                type="date"
              />
            </div>

            <div class="form-group">
              <label>Priority</label>

              <select v-model="form.priority">
                <option>Low</option>
                <option>Medium</option>
                <option>High</option>
              </select>
            </div>

            <div class="form-group">
              <label>Status</label>

              <select v-model="form.status">
                <option>Pending</option>
                <option>In Progress</option>
                <option>Completed</option>
              </select>
            </div>

            <div class="form-group full">
              <label>Description *</label>

              <textarea
                v-model="form.description"
                rows="4"
                placeholder="Enter task description"
              ></textarea>
            </div>

          </div>

          <div class="form-buttons">

            <button
              type="submit"
              class="btn primary"
            >
              {{ editingId ? '💾 Update Task' : '➕ Add Task' }}
            </button>

            <button
              v-if="editingId"
              type="button"
              class="btn secondary"
              @click="resetForm"
            >
              Cancel
            </button>

          </div>

        </form>
      </section>

      <section class="card">

        <div class="list-header">

          <div>
            <h2>📚 My Tasks</h2>
            <p>{{ filteredTasks.length }} task(s) found</p>
          </div>

          <input
            v-model="search"
            class="search"
            type="text"
            placeholder="🔍 Search tasks..."
          />

        </div>

        <div
          v-if="filteredTasks.length === 0"
          class="empty"
        >
          <div class="empty-icon">📭</div>
          <h3>No tasks found</h3>
          <p>Add a new task to get started.</p>
        </div>

        <div
          v-else
          class="task-list"
        >

          <div
            v-for="task in filteredTasks"
            :key="task.id"
            class="task"
          >

            <div class="task-main">

              <div class="task-title">

                <h3>{{ task.title }}</h3>

                <span
                  class="badge priority"
                  :class="getPriorityClass(task.priority)"
                >
                  {{ task.priority }}
                </span>

                <span
                  class="badge status"
                  :class="getStatusClass(task.status)"
                >
                  {{ task.status }}
                </span>

              </div>

              <p class="subject">
                📘 {{ task.subject }}
              </p>

              <p class="description">
                {{ task.description }}
              </p>

              <p class="date">
                📅 Due: {{ task.dueDate }}
              </p>

            </div>

            <div class="actions">

              <button
                class="edit"
                @click="editTask(task)"
              >
                ✏️ Edit
              </button>

              <button
                class="delete"
                @click="deleteTask(task.id)"
              >
                🗑️ Delete
              </button>

            </div>

          </div>

        </div>

      </section>

    </main>

    <footer>
      <p>Student Task Management System • Vue 3 CRUD</p>
    </footer>

  </div>
</template>
