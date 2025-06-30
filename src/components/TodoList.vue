<template>
  <div class="todo-list">
    <div v-if="todos.length > 0" class="list-header">
      <p class="todo-count">
        {{ completedCount }} de {{ todos.length }} tarefas concluídas
      </p>
      <button 
        v-if="completedCount > 0" 
        @click="clearCompleted" 
        class="btn btn-secondary"
      >
        Limpar Concluídas
      </button>
    </div>
    
    <transition-group name="todo" tag="ul" class="todos">
      <TodoItem
        v-for="todo in todos"
        :key="todo.id"
        :todo="todo"
        @toggle="$emit('toggle-todo', todo.id)"
        @remove="$emit('remove-todo', todo.id)"
        @edit="$emit('edit-todo', todo.id, $event)"
      />
    </transition-group>
  </div>
</template>

<script>
import { computed } from 'vue'
import TodoItem from './TodoItem.vue'

export default {
  name: 'TodoList',
  components: {
    TodoItem
  },
  props: {
    todos: {
      type: Array,
      required: true
    }
  },
  emits: ['toggle-todo', 'remove-todo', 'edit-todo'],
  setup(props, { emit }) {
    const completedCount = computed(() => 
      props.todos.filter(todo => todo.completed).length
    )

    const clearCompleted = () => {
      props.todos
        .filter(todo => todo.completed)
        .forEach(todo => emit('remove-todo', todo.id))
    }

    return {
      completedCount,
      clearCompleted
    }
  }
}
</script>
