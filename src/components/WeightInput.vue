<script setup>
import { ref } from 'vue'

const emit = defineEmits(['add-weight'])

const weight = ref('')

// Helper to get local ISO string like "YYYY-MM-DDTHH:MM"
const getLocalISODatetime = () => {
    const now = new Date()
    now.setMinutes(now.getMinutes() - now.getTimezoneOffset())
    return now.toISOString().slice(0, 16)
}

const dateTime = ref(getLocalISODatetime())

const handleSubmit = () => {
  if (!weight.value || !dateTime.value) return

  // Create Date object from local value
  const d = new Date(dateTime.value)

  emit('add-weight', {
    weight: parseFloat(weight.value),
    date: d.toISOString(), 
    id: Date.now()
  })

  // Reset weight, update time to now
  weight.value = ''
  dateTime.value = getLocalISODatetime()
}
</script>

<template>
  <form @submit.prevent="handleSubmit" class="input-form">
    <div class="input-group">
      <input 
        type="number" 
        step="0.1" 
        v-model="weight" 
        placeholder="Weight (kg)" 
        required
        class="weight-input"
      />
      <input 
        type="datetime-local" 
        v-model="dateTime" 
        required 
        class="date-input"
      />
    </div>
    <button type="submit">Add</button>
  </form>
</template>

<style scoped>
.input-form {
  display: flex;
  flex-direction: row; /* Horizontal */
  gap: 1rem;
  align-items: center;
  width: 100%;
}

.input-group {
  display: flex;
  gap: 0.5rem;
  flex: 1;
}

.weight-input, .date-input, .time-input {
  flex: 1;
  padding: 0.6em; /* Smaller padding */
  min-width: 0;
}

button {
  padding: 0.6em 1.2em;
  white-space: nowrap;
}

@media (max-width: 600px) {
  .input-form {
    flex-direction: column;
    align-items: stretch;
  }
  .input-group {
    flex-direction: column;
  }
}
</style>
