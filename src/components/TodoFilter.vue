<template>
  <div class="todo-filter">
    <div class="filter-container">
      <div class="search-box">
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Pesquisar tarefas..."
          class="search-input"
          @input="emitFilterChange"
        />
        <span class="search-icon">🔍</span>
      </div>
      
      <div class="filter-options">
        <select v-model="statusFilter" @change="emitFilterChange" class="status-select">
          <option value="all">Todas as tarefas</option>
          <option value="completed">Concluídas</option>
          <option value="active">Pendentes</option>
        </select>
        
        <div class="priority-filter">
          <span class="filter-label">Prioridade:</span>
          <div class="priority-buttons">
            <button 
              @click="togglePriorityFilter('high')" 
              :class="['priority-btn', {'active': priorityFilter.includes('high')}]"
            >
              Alta
            </button>
            <button 
              @click="togglePriorityFilter('medium')" 
              :class="['priority-btn', {'active': priorityFilter.includes('medium')}]"
            >
              Média
            </button>
            <button 
              @click="togglePriorityFilter('low')" 
              :class="['priority-btn', {'active': priorityFilter.includes('low')}]"
            >
              Baixa
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue'

export default {
  name: 'TodoFilter',
  emits: ['filter-change'],
  setup(props, { emit }) {
    const searchQuery = ref('')
    const statusFilter = ref('all')
    const priorityFilter = ref([])

    const togglePriorityFilter = (priority) => {
      const index = priorityFilter.value.indexOf(priority)
      if (index === -1) {
        priorityFilter.value.push(priority)
      } else {
        priorityFilter.value.splice(index, 1)
      }
      emitFilterChange()
    }

    const emitFilterChange = () => {
      emit('filter-change', {
        search: searchQuery.value,
        status: statusFilter.value,
        priorities: priorityFilter.value
      })
    }

    return {
      searchQuery,
      statusFilter,
      priorityFilter,
      togglePriorityFilter,
      emitFilterChange
    }
  }
}
</script>
