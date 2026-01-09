<script setup>
import { computed } from 'vue'

const props = defineProps({
  weights: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['delete-weight'])

// Sort descending by date (using full ISO string comparison is sufficient and accurate)
const sortedWeights = computed(() => {
  return [...props.weights].sort((a, b) => new Date(b.date) - new Date(a.date))
})

const formatDate = (isoString) => {
  const d = new Date(isoString)
  return d.toLocaleDateString(undefined, { month: 'short', day: 'numeric' })
}

const formatTime = (isoString) => {
  const d = new Date(isoString)
  // Check if it's a legacy date-only string (length 10 e.g. YYYY-MM-DD)
  if (isoString.length <= 10) return ''
  return d.toLocaleTimeString(undefined, { hour: '2-digit', minute: '2-digit' })
}
</script>

<template>
  <div class="history-list">
    <div v-if="weights.length === 0" class="empty-state">
      No initial entries yet. Start tracking!
    </div>
    <div v-else class="list-container">
      <div v-for="entry in sortedWeights" :key="entry.id" class="history-item">
        <div class="info">
            <span class="date">
                {{ formatDate(entry.date) }}
                <small v-if="formatTime(entry.date)" class="time">{{ formatTime(entry.date) }}</small>
            </span>
            <span class="weight">{{ entry.weight }} <small>kg</small></span>
        </div>
        <button class="delete-btn" @click="$emit('delete-weight', entry.id)" title="Delete entry">
            &times;
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.history-list {
  text-align: left;
  display: flex;
  flex-direction: column;
  height: 100%; /* Fill parent */
  overflow: hidden; /* Prevent double scroll */
}

.list-container {
  flex: 1; /* Take remaining space */
  overflow-y: auto; /* Scroll internally */
  padding-right: 0.2rem;
  min-height: 0; /* Important for nested flex scroll */
}

.history-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.8rem 0.5rem;
  border-bottom: 1px solid #f1f5f9;
  transition: background 0.2s;
}

.info {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex: 1;
    margin-right: 1rem;
}

.history-item:hover {
  background: #f8fafc;
}



.history-item:last-child {
  border-bottom: none;
}

.date {
  color: #64748b;
  font-size: 0.95em;
  font-variant-numeric: tabular-nums;
  display: flex;
  flex-direction: row; /* Inline */
  align-items: center;
  gap: 0.5rem;
  text-align: left;
}

.time {
    font-size: 0.85em; /* Slightly bigger */
    color: #94a3b8;
    font-weight: 500;
}

.weight {
  font-weight: 600;
  color: #0f172a;
  font-size: 1.1em;
  font-variant-numeric: tabular-nums;
}

.weight small {
  color: #94a3b8;
  font-weight: 400;
  font-size: 0.8em;
  margin-left: 2px;
}

.delete-btn {
    background: transparent;
    color: #cbd5e1; /* Visible but subtle */
    border: none;
    font-size: 1.2rem;
    cursor: pointer;
    box-shadow: none;
    padding: 0 0.5rem;
    line-height: 1;
    transition: color 0.2s;
}

.delete-btn:hover {
    color: #ef4444;
    background: transparent;
    transform: none;
}

.empty-state {
  text-align: center;
  color: #94a3b8;
  padding: 3rem 1rem;
  font-style: italic;
}
</style>
