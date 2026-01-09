<script setup>
import { computed, ref, watch, onMounted, nextTick } from 'vue'
import Chart from 'chart.js/auto'

const props = defineProps({
  weights: {
    type: Array, // [{ weight: Number, date: String }]
    required: true
  }
})

const chartCanvas = ref(null)
let chartInstance = null

// Prepare data for Chart.js
const chartData = computed(() => {
  // Sort by date ascending
  const sorted = [...props.weights].sort((a, b) => new Date(a.date) - new Date(b.date))
  return {
    labels: sorted.map(w => w.date),
    datasets: [{
      label: 'Weight (kg)',
      data: sorted.map(w => w.weight),
      borderColor: '#a855f7', // Purple-ish
      backgroundColor: 'rgba(168, 85, 247, 0.2)',
      borderWidth: 3,
      tension: 0.4, // Smooth curves
      pointBackgroundColor: '#ec4899',
      pointBorderColor: '#fff',
      pointHoverBackgroundColor: '#fff',
      pointHoverBorderColor: '#ec4899',
      fill: true
    }]
  }
})

const renderChart = () => {
    if (chartInstance) {
        chartInstance.destroy()
    }
    
    if (!chartCanvas.value) return;

    const ctx = chartCanvas.value.getContext('2d')

    chartInstance = new Chart(ctx, {
        type: 'line',
        data: chartData.value,
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
                legend: {
                    display: false
                },
                tooltip: {
                    backgroundColor: 'rgba(15, 23, 42, 0.9)',
                    titleColor: '#fff',
                    bodyColor: '#cbd5e1',
                    padding: 10,
                    cornerRadius: 8,
                    displayColors: false
                }
            },
            scales: {
                x: {
                    grid: {
                        color: 'rgba(255, 255, 255, 0.05)'
                    },
                    ticks: {
                        color: '#94a3b8'
                    }
                },
                y: {
                    grid: {
                        color: 'rgba(255, 255, 255, 0.05)'
                    },
                    ticks: {
                        color: '#94a3b8'
                    }
                }
            }
        }
    })
}

watch(() => props.weights, () => {
    // Re-render or update chart when data changes
    if (chartInstance) {
        chartInstance.data = chartData.value
        chartInstance.update()
    } else {
        renderChart()
    }
}, { deep: true })

onMounted(() => {
    renderChart()
})
</script>

<template>
  <div class="chart-container">
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>

<style scoped>
.chart-container {
  position: relative;
  height: 300px; /* Fixed height for the chart area */
  width: 100%;
}
</style>
