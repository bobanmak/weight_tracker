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
  const sorted = [...props.weights].sort((a, b) => new Date(a.date) - new Date(b.date) || a.id - b.id)
  
  if (sorted.length < 2) {
     return {
        labels: sorted.map(w => w.date),
        datasets: [{
          label: 'Weight (kg)',
          data: sorted.map(w => w.weight),
          borderColor: '#a855f7',
          backgroundColor: 'rgba(168, 85, 247, 0.2)',
          borderWidth: 3,
          tension: 0.4,
          pointBackgroundColor: '#ec4899',
          pointBorderColor: '#fff',
          pointHoverBackgroundColor: '#fff',
          pointHoverBorderColor: '#ec4899',
          fill: true
        }]
     }
  }

  // Linear Regression Calculation
  const n = sorted.length
  let sumX = 0
  let sumY = 0
  let sumXY = 0
  let sumXX = 0

  // Use index as X for simplicity in uniform time intervals, 
  // or use timestamp for accurate time-series regression.
  // Using timestamp is better if entries are irregular.
  const points = sorted.map(w => ({ x: new Date(w.date).getTime(), y: w.weight }))
  
  // Normalize X to avoid huge numbers (start from 0)
  const minX = points[0].x
  const normalizedPoints = points.map(p => ({ x: p.x - minX, y: p.y }))

  for (let i = 0; i < n; i++) {
    sumX += normalizedPoints[i].x
    sumY += normalizedPoints[i].y
    sumXY += normalizedPoints[i].x * normalizedPoints[i].y
    sumXX += normalizedPoints[i].x * normalizedPoints[i].x
  }

  const slope = (n * sumXY - sumX * sumY) / (n * sumXX - sumX * sumX)
  const intercept = (sumY - slope * sumX) / n

  const trendData = normalizedPoints.map(p => slope * p.x + intercept)

  return {
    labels: sorted.map(w => {
        // Format ISO string to short date
        const d = new Date(w.date)
        return d.toLocaleDateString(undefined, { month: 'short', day: 'numeric' })
    }),
    datasets: [
      {
        label: 'Weight (kg)',
        data: sorted.map(w => w.weight),
        borderColor: '#0284c7', // Sky 600
        backgroundColor: 'rgba(2, 132, 199, 0.1)',
        borderWidth: 3,
        tension: 0.4,
        pointBackgroundColor: '#fff',
        pointBorderColor: '#0284c7',
        pointHoverBackgroundColor: '#0284c7',
        pointHoverBorderColor: '#fff',
        pointRadius: 4,
        pointHoverRadius: 6,
        fill: true,
        order: 1
      },
      {
        label: 'Trend',
        data: trendData,
        borderColor: '#94a3b8', // Slate 400
        borderWidth: 2,
        borderDash: [5, 5],
        pointRadius: 0,
        fill: false,
        tension: 0,
        order: 2
      }
    ]
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
                    backgroundColor: '#1e293b',
                    titleColor: '#fff',
                    bodyColor: '#cbd5e1',
                    padding: 12,
                    cornerRadius: 8,
                    displayColors: false,
                    titleFont: {
                        size: 14,
                        weight: 'bold'
                    },
                    bodyFont: {
                        size: 13
                    }
                }
            },
            scales: {
                x: {
                    grid: {
                        color: '#f1f5f9',
                        drawBorder: false
                    },
                    ticks: {
                        color: '#64748b',
                        font: {
                            family: "'Inter', sans-serif"
                        }
                    }
                },
                y: {
                    grid: {
                        color: '#f1f5f9',
                        drawBorder: false
                    },
                    ticks: {
                        color: '#64748b',
                        font: {
                            family: "'Inter', sans-serif"
                        }
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
  flex: 1; /* Grow to fill section */
  min-height: 0; /* Allow shrinking */
  width: 100%;
  display: flex;
  flex-direction: column;
}
canvas {
    flex: 1;
}
</style>
