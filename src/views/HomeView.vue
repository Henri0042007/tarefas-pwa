<template>
  <div>
    <p v-if="store.error" class="error-message">
      {{ store.error }}
      <button @click="store.fetchTasks()">Tentar novamente</button>
    </p>

    <input
      v-model="store.filterText"
      type="text"
      placeholder="Buscar tarefas..."
      class="filter-input"
    />

    <TaskForm
      :editing-task="editingTask"
      @add="handleAdd"
      @update="handleUpdate"
      @cancel="handleCancel"
    />

    <p v-if="store.loading" class="loading-message">Carregando tarefas...</p>

    <template v-else>
      <section v-if="store.filteredPendingTasks.length > 0">
        <h2 class="section-title">
          Pendentes (
          {{ store.filteredPendingTasks.length }})
        </h2>

        <TaskItem
          v-for="task in store.filteredPendingTasks"
          :key="task.id"
          :task="task"
          @toggle="handleToggle"
          @remove="handleRemove"
          @edit="handleEdit"
        />
      </section>

      <section v-if="store.filteredCompletedTasks.length > 0">
        <h2 class="section-title">
          Concluídas (
          {{ store.filteredCompletedTasks.length }})
        </h2>

        <TaskItem
          v-for="task in store.filteredCompletedTasks"
          :key="task.id"
          :task="task"
          @toggle="handleToggle"
          @remove="handleRemove"
          @edit="handleEdit"
        />
      </section>

      <p v-if="store.tasks.length === 0" class="empty-message">
        Nenhuma tarefa cadastrada. Adicione uma acima.
      </p>
    </template>

    <InstallButton />
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import TaskForm from '../components/TaskForm.vue'
import TaskItem from '../components/TaskItem.vue'
import InstallButton from '../components/InstallButton.vue'
import { useTasksStore } from '../stores/tasks.js'

const store = useTasksStore()
const editingTask = ref(null)

onMounted(() => {
  store.fetchTasks()
})

function handleAdd(title, priority) {
  store.addTask(title, priority)
}

function handleUpdate(id, title, imgAttachmentKey) {
  store.updateTask(id, { title, imgAttachmentKey })
  editingTask.value = null
}

function handleCancel() {
  editingTask.value = null
}

function handleEdit(task) {
  editingTask.value = task
}

function handleToggle(id) {
  store.toggleTask(id)
}

function handleRemove(id) {
  if (editingTask.value?.id === id) editingTask.value = null
  store.removeTask(id)
}
</script>

<style scoped>
.section-title {
  font-size: 1rem;
  color: #666;
  margin-bottom: 12px;
  margin-top: 20px;
}

.empty-message {
  text-align: center;
  color: #999;
  margin-top: 40px;
  font-size: 0.95rem;
}

.error-message {
  color: #c0392b;
  background-color: #fdecea;
  border: 1px solid #e74c3c;
  border-radius: 6px;
  padding: 10px 14px;
  margin-bottom: 12px;
  font-size: 0.9rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.error-message button {
  background: none;
  border: 1px solid #e74c3c;
  color: #c0392b;
  padding: 4px 8px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.8rem;
}

.error-message button:hover {
  background-color: #e74c3c;
  color: white;
}

.filter-input {
  width: 100%;
  padding: 12px;
  border: 2px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  margin-bottom: 12px;
  outline: none;
}

.filter-input:focus {
  border-color: #4a90d9;
}

.loading-message {
  color: #666;
  font-size: 0.9rem;
  padding: 8px 0;
}
</style>
