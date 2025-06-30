<template>
  <form @submit.prevent="handleSubmit" class="todo-form">
    <div class="form-group">
      <input
        v-model="newTodo"
        type="text"
        placeholder="Nova tarefa..."
        class="todo-input"
        maxlength="100"
        required
      />
      <select v-model="priority" class="priority-select">
        <option value="high">Alta</option>
        <option value="medium" selected>Média</option>
        <option value="low">Baixa</option>
      </select>
      <button type="submit" class="btn btn-primary" :disabled="!newTodo.trim()">
        Adicionar
      </button>
    </div>
  </form>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'TodoForm',
  emits: ['add-todo'],
  setup(props, { emit }) {
    const newTodo = ref('')
    const priority = ref('medium')

    const handleSubmit = () => {
      if (newTodo.value.trim()) {
        emit('add-todo', {
          text: newTodo.value.trim(),
          priority: priority.value
        })
        newTodo.value = ''
        priority.value = 'medium'
      }
    }

    return {
      newTodo,
      priority,
      handleSubmit
    }
  }
}
</script>
