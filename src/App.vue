<template>
  <div id="app">
    <div class="container">
      <h1>To-Do List</h1>
      <TodoForm @add-todo="addTodo" />
      <TodoFilter @filter-change="applyFilters" />
      <TodoList 
        :todos="filteredTodos" 
        @toggle-todo="toggleTodo"
        @remove-todo="removeTodo"
        @edit-todo="editTodo"
      />
      <div v-if="filteredTodos.length === 0" class="empty-state">
        <p v-if="todos.length === 0">Nenhuma tarefa adicionada ainda!</p>
        <p v-else>Nenhuma tarefa corresponde aos filtros aplicados.</p>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import TodoForm from './components/TodoForm.vue'
import TodoList from './components/TodoList.vue'
import TodoFilter from './components/TodoFilter.vue'

export default {
  name: 'App',
  components: {
    TodoForm,
    TodoList,
    TodoFilter
  },
  setup() {
    const todos = ref([])
    const filters = ref({
      search: '',
      status: 'all',
      priorities: []
    })

    // Carregar dados do localStorage
    const loadTodos = () => {
      const saved = localStorage.getItem('vue-todos')
      if (saved) {
        todos.value = JSON.parse(saved)
      }
    }

    // Salvar no localStorage
    const saveTodos = () => {
      localStorage.setItem('vue-todos', JSON.stringify(todos.value))
    }

    // Adicionar nova tarefa (com prioridade)
    const addTodo = (todoData) => {
      if (todoData.text.trim()) {
        todos.value.push({
          id: Date.now(),
          text: todoData.text,
          completed: false,
          priority: todoData.priority || 'medium',
          createdAt: new Date().toISOString()
        })
        saveTodos()
      }
    }

    // Alternar status da tarefa
    const toggleTodo = (id) => {
      const todo = todos.value.find(t => t.id === id)
      if (todo) {
        todo.completed = !todo.completed
        saveTodos()
      }
    }

    // Remover tarefa
    const removeTodo = (id) => {
      todos.value = todos.value.filter(t => t.id !== id)
      saveTodos()
    }

    // Editar tarefa
    const editTodo = (id, newText) => {
      const todo = todos.value.find(t => t.id === id)
      if (todo && newText.trim()) {
        todo.text = newText.trim()
        saveTodos()
      }
    }

    // Aplicar filtros
    const applyFilters = (newFilters) => {
      filters.value = newFilters
    }

    // Computed property para tarefas filtradas
    const filteredTodos = computed(() => {
      return todos.value.filter(todo => {
        // Filtro de texto
        const matchesSearch = todo.text.toLowerCase().includes(filters.value.search.toLowerCase())
        
        // Filtro de status
        const matchesStatus = 
          filters.value.status === 'all' || 
          (filters.value.status === 'completed' && todo.completed) ||
          (filters.value.status === 'active' && !todo.completed)
        
        // Filtro de prioridade
        const matchesPriority = 
          filters.value.priorities.length === 0 || 
          filters.value.priorities.includes(todo.priority)
        
        return matchesSearch && matchesStatus && matchesPriority
      })
    })

    // Carregar dados na inicialização
    loadTodos()

    return {
      todos,
      filteredTodos,
      addTodo,
      toggleTodo,
      removeTodo,
      editTodo,
      applyFilters
    }
  }
}
</script>
