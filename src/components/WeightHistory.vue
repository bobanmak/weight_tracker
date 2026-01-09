<script setup>
import { computed } from 'vue'

const props = defineProps({
  weights: {
    type: Array,
    required: true
  }
})

// Sort descending by date for history
const sortedWeights = computed(() => {
  return [...props.weights].sort((a, b) => new Date(b.date) - new Date(a.date))
})
</script>

<template>
  <div class="history-list">
    <div v-if="weights.length === 0" class="empty-state">
      No initial entries yet. Start tracking!
    </div>
    <div v-else class="list-container">
      <div v-for="entry in sortedWeights" :key="entry.id" class="history-item">
        <span class="date">{{ entry.date }}</span>
        <span class="weight">{{ entry.weight }} <small>kg</small></span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.history-list {
  text-align: left;
}

.list-container {
  max-height: 250px;
  overflow-y: auto;
  padding-right: 0.5rem;
}

.history-item {
  display: flex;
  justify-content: space-between;
  padding: 0.8rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  transition: background 0.2s;
}

.history-item:hover {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 8px;
}

.history-item:last-child {
  border-bottom: none;
}

.date {
  color: #94a3b8;
  font-size: 0.9em;
}

.weight {
  font-weight: 700;
  color: #ec4899; /* Pink accent */
}

.empty-state {
  text-align: center;
  color: #64748b;
  padding: 2rem;
}
</style>
