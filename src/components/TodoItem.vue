<template>
  <li class="todo-item" :class="{ completed: todo.completed, ['priority-' + todo.priority]: true }">
    <div class="todo-content">
      <input
        type="checkbox"
        :checked="todo.completed"
        @change="$emit('toggle')"
        class="todo-checkbox"
      />
      
      <div class="todo-details">
        <span 
          v-if="!isEditing"
          @dblclick="startEdit"
          class="todo-text"
          :class="{ 'completed-text': todo.completed }"
        >
          {{ todo.text }}
        </span>
        
        <input
          v-else
          v-model="editText"
          @blur="finishEdit"
          @keyup.enter="finishEdit"
          @keyup.esc="cancelEdit"
          class="edit-input"
          ref="editInput"
        />
        
        <div class="todo-meta">
          <span class="priority-badge" :class="'priority-' + todo.priority">
            {{ getPriorityLabel(todo.priority) }}
          </span>
        </div>
      </div>
    </div>
    
    <div class="todo-actions">
      <button 
        v-if="!isEditing"
        @click="startEdit" 
        class="btn btn-edit"
        title="Editar"
      >
        ✏️
      </button>
      <button 
        @click="$emit('remove')" 
        class="btn btn-remove"
        title="Remover"
      >
        🗑️
      </button>
    </div>
  </li>
</template>

<script>
import { ref, nextTick } from 'vue'

export default {
  name: 'TodoItem',
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  emits: ['toggle', 'remove', 'edit'],
  setup(props, { emit }) {
    const isEditing = ref(false)
    const editText = ref('')
    const editInput = ref(null)

    const startEdit = () => {
      isEditing.value = true
      editText.value = props.todo.text
      nextTick(() => {
        editInput.value?.focus()
      })
    }

    const finishEdit = () => {
      if (editText.value.trim() && editText.value !== props.todo.text) {
        emit('edit', editText.value.trim())
      }
      isEditing.value = false
    }

    const cancelEdit = () => {
      isEditing.value = false
      editText.value = props.todo.text
    }
    
    const getPriorityLabel = (priority) => {
      const labels = {
        high: 'Alta',
        medium: 'Média',
        low: 'Baixa'
      }
      return labels[priority] || 'Média'
    }

    return {
      isEditing,
      editText,
      editInput,
      startEdit,
      finishEdit,
      cancelEdit,
      getPriorityLabel
    }
  }
}
</script>
