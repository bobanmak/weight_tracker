<script setup>
import { ref, onMounted, watch } from 'vue'
import WeightChart from './components/WeightChart.vue'
import WeightInput from './components/WeightInput.vue'
import WeightHistory from './components/WeightHistory.vue'
import WeightStats from './components/WeightStats.vue'

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

const deleteWeight = (id) => {
  weights.value = weights.value.filter(w => w.id !== id)
}
</script>

<template>
  <div class="app-container">
    <!-- Top Bar: Header & Stats -->
    <div class="top-bar card">
      <header>
        <div class="logo-wrapper">
            <!-- Sporty Activity/Pulse Icon -->
            <svg class="app-logo" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                <path d="M22 12h-4l-3 9L9 3l-3 9H2"/>
            </svg>
            <div>
                <h1>Weight Tracker</h1>
                <p class="subtitle">Monitor your progress</p>
            </div>
        </div>
      </header>
      <div v-if="weights.length > 0" class="mini-stats">
        <WeightStats :weights="weights" />
      </div>
    </div>

    <!-- Middle: Input Bar -->
    <section class="input-bar card">
      <WeightInput @add-weight="addWeight" />
    </section>

    <!-- Bottom: Main Grid -->
    <main class="grid-layout">
      <section class="chart-section card">
        <h2>Progress</h2>
        <WeightChart :weights="weights" />
      </section>

      <section class="history-section card">
        <h2>History</h2>
        <WeightHistory :weights="weights" @delete-weight="deleteWeight" />
      </section>
    </main>
  </div>
</template>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  gap: 1rem; /* Tighter gap */
  height: 100vh;
  box-sizing: border-box;
  overflow: hidden; /* Try to avoid window scroll */
  padding-bottom: 1rem;
}

.top-bar {
  display: flex;
  flex-wrap: nowrap; /* Keep row */
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  margin-bottom: 0;
  gap: 1rem; 
}

.top-bar header {
  text-align: left;
}

.logo-wrapper {
    display: flex;
    align-items: center;
    gap: 0.8rem;
}

.app-logo {
    width: 32px;
    height: 32px;
    color: var(--primary-color);
    flex-shrink: 0;
}

.top-bar h1 {
  font-size: 1.5rem;
  margin: 0;
  line-height: 1.2;
}

.subtitle {
  margin: 0;
  font-size: 0.9rem;
}

.input-bar {
  padding: 1rem 2rem;
  margin-bottom: 0;
}

.grid-layout {
  display: grid;
  grid-template-columns: 2fr 1fr; /* Chart bigger than History */
  gap: 1rem;
  flex: 1; /* Take remaining height */
  min-height: 0; /* Allow grid to shrink/scroll internally */
}

.chart-section, .history-section {
  margin-bottom: 0;
  display: flex;
  flex-direction: column;
  overflow: hidden; /* Internal scrolling if needed */
}

/* Adjust h2 for compact view */
h2 {
  font-size: 1.1rem;
  margin-bottom: 0.5rem;
}

@media (max-width: 900px) {
  .app-container {
    height: auto;
    overflow-y: auto;
  }
  .top-bar {
    flex-direction: column;
    gap: 1rem;
    align-items: stretch;
  }
  .grid-layout {
    grid-template-columns: 1fr;
  }
}
</style>
