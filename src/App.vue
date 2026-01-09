<script setup>
import { ref, onMounted, watch } from 'vue'
import WeightChart from './components/WeightChart.vue'
import WeightInput from './components/WeightInput.vue'
import WeightHistory from './components/WeightHistory.vue'

const weights = ref([])

onMounted(() => {
  const saved = localStorage.getItem('weight-tracker-data')
  if (saved) {
    weights.value = JSON.parse(saved)
  }
})

watch(weights, (newVal) => {
  localStorage.setItem('weight-tracker-data', JSON.stringify(newVal))
}, { deep: true })

const addWeight = (entry) => {
  weights.value = [...weights.value, entry]
}
</script>

<template>
  <div class="app-container">
    <header>
      <h1>Weight Tracker</h1>
      <p class="subtitle">Monitor your progress, reach your goals.</p>
    </header>

    <main class="grid-layout">
      <section class="chart-section card">
        <h2>Progress Over Time</h2>
        <WeightChart :weights="weights" />
      </section>

      <div class="sidebar">
        <section class="input-section card">
          <h2>Add New Entry</h2>
          <WeightInput @add-weight="addWeight" />
        </section>

        <section class="history-section card">
          <h2>History</h2>
          <WeightHistory :weights="weights" />
        </section>
      </div>
    </main>
  </div>
</template>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.subtitle {
  color: #94a3b8;
  margin-top: -1.5rem;
  margin-bottom: 3rem;
  font-size: 1.1rem;
}

.grid-layout {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 2rem;
  align-items: start;
}

@media (max-width: 900px) {
  .grid-layout {
    grid-template-columns: 1fr;
  }
}

h2 {
  font-size: 1.2rem;
  margin-top: 0;
  margin-bottom: 1rem;
  color: rgba(255, 255, 255, 0.9);
  text-align: left;
}
</style>
