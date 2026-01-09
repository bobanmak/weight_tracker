<script setup>
import { computed } from 'vue'

const props = defineProps({
  weights: {
    type: Array,
    required: true
  }
})

const stats = computed(() => {
  if (props.weights.length === 0) return null
  
  // Sort by date descending
  const sorted = [...props.weights].sort((a, b) => new Date(b.date) - new Date(a.date) || b.id - a.id)
  const current = sorted[0]
  const previous = sorted[1]
  const start = sorted[sorted.length - 1] // Earliest entry

  let diffLast = 0
  let isLossLast = true
  
  if (previous) {
    diffLast = current.weight - previous.weight
    isLossLast = diffLast <= 0
  }

  // Calculate total change
  const diffStart = current.weight - start.weight
  const isLossStart = diffStart <= 0

  return {
    current: current.weight,
    
    diffLast: Math.abs(diffLast).toFixed(1),
    isLossLast,
    signLast: diffLast > 0 ? '+' : (diffLast < 0 ? '-' : ''),

    diffStart: Math.abs(diffStart).toFixed(1),
    isLossStart,
    signStart: diffStart > 0 ? '+' : (diffStart < 0 ? '-' : '')
  }
})
</script>

<template>
  <div v-if="stats" class="stats-container card">
    <div class="stat-item">
      <span class="label">Current</span>
      <span class="value">{{ stats.current }}<small>kg</small></span>
    </div>
    
    <div class="stat-item">
      <span class="label">Change</span>
      <span class="value diff" :class="{ loss: stats.isLossLast, gain: !stats.isLossLast }">
        {{ stats.signLast }}{{ stats.diffLast }}<small>kg</small>
      </span>
    </div>

    <div class="stat-item">
      <span class="label">Total</span>
      <span class="value diff" :class="{ loss: stats.isLossStart, gain: !stats.isLossStart }">
        {{ stats.signStart }}{{ stats.diffStart }}<small>kg</small>
      </span>
    </div>
  </div>
</template>

<style scoped>
.stats-container {
  display: flex;
  flex-wrap: nowrap; /* Force single line */
  align-items: center;
  gap: 0.8rem; /* Tight gap */
  padding: 0;
  margin: 0;
  background: transparent;
  box-shadow: none;
  border: none;
}

.stat-item {
  display: flex;
  flex-direction: row; 
  align-items: baseline;
  gap: 0.3rem; 
  white-space: nowrap;
}

.label {
  font-size: 0.5rem;
  font-weight: 600;
  color: #94a3b8;
  text-transform: uppercase;
  /* Always visible */
}

.value {
  font-size: 1.1rem; 
  font-weight: 700;
  color: #334155;
  line-height: 1;
}

.value small {
  font-size: 0.75rem;
  margin-left: 1px;
  font-weight: 500;
  color: #94a3b8;
}

.diff.loss {
  color: #10b981;
}

.diff.gain {
  color: #ef4444;
}

@media (max-width: 400px) {
  .stats-container {
    gap: 0.5rem;
  }
}
</style>
