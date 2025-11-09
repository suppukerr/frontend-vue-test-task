<template>
  <div class='text-input'>
    <input
      :id='field.model'
      :type='field.type'
      :value='modelValue'
      :placeholder="field.label"
      :class="['input', { 'input-error': hasError }]"
      @input='handleInput'
      @blur='handleBlur'
    />
  </div>
  <div v-if="filteredErrors.length > 0" class="error-messages">
    <div
        v-for="errorMessage in filteredErrors"
        :key="errorMessage"
        class="error-message"
    >
        {{ errorMessage }}
    </div>
  </div>
  <!-- <div v-if='error && error.length > 0' class='error-messages'>
      <div v-for='errorMessage in error' :key='errorMessage' class='error-message'>
        {{ errorMessage }}
      </div>
  </div> -->
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  field: {
    type: Object,
    required: true,
  },
  modelValue: {
    type: [String, Number],
    default: '',
  },
  error: {
    type: Array,
    default: null,
  },
  hasError: Boolean,
})

const emit = defineEmits(['update:modelValue', 'validate'])

const handleInput = (event) => {
  emit('update:modelValue', event.target.value)
}

const handleBlur = (event) => {
  emit('validate', event.target.value)
}

const filteredErrors = computed(() =>
  props.error?.filter(msg => msg !== "Это поле обязательное для заполнения") ?? []
)
</script>

<style scoped>
.input-error {
  border-color: #6b21acff !important;
  background-color: rgba(107, 33, 172, 0.07) !important;
}

.input-error:focus {
  border-color: #6b21acff !important;
  box-shadow: 0 0 0 2px rgba(107, 33, 172, 0.2) !important;
}

.input::placeholder {
  color: #00000096 !important;
  opacity: 1;
}

/*Звездочки при ошибке */
.has-error {
  color: #6b21acff;
  font-weight: bold;
}

.error-messages {
  margin-top: 5px;
}

.error-message {
  color: #6b21acff;
  font-size: 12px;
  margin-bottom: 2px;
}

.input {
  width: 100%;
  padding: 10px 12px;
  border: 1.8px solid #000000ff;
  border-radius: 0;
  font-size: 14px;
  transition: border-color 0.3s, background-color 0.3s;
  box-sizing: border-box;
}

.input:focus {
  outline: none;
  border-color: #3acfcf;
  box-shadow: 0 0 0 2px rgba(58, 207, 207, 0.2);
}

.label {
  display: block;
  margin-bottom: 5px;
  font-weight: 500;
  color: #000000ff;
  font-size: 14px;
}
</style>