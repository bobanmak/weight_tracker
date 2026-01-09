<script setup>
import { ref } from 'vue'

const emit = defineEmits(['add-weight'])

const weight = ref('')
const date = ref(new Date().toISOString().split('T')[0]) // Default to today

const handleSubmit = () => {
  if (!weight.value || !date.value) return

  emit('add-weight', {
    weight: parseFloat(weight.value),
    date: date.value,
    id: Date.now() // Simple unique ID
  })

  // Reset weight but keep date (user might input multiple for same day or next day)
  weight.value = ''
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
        type="date" 
        v-model="date" 
        required 
        class="date-input"
      />
    </div>
    <button type="submit">Add Entry</button>
  </form>
</template>

<style scoped>
.input-form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: stretch;
}

.input-group {
  display: flex;
  gap: 1rem;
}

.weight-input {
  flex: 1;
}

.date-input {
  flex: 1;
}

@media (max-width: 600px) {
  .input-group {
    flex-direction: column;
  }
}
</style>
