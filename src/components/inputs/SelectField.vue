<template>
  <div class='select-input'>
    <select
      :id='field.model'
      :value='modelValue'
      :class="[
      'select',
      { 'select-error': error },
      { 'select-placeholder': modelValue === '' }
    ]"
      @change='handleChange'
      @blur='handleBlur'
    >
    <option value='' disabled>{{ field.label }}</option>
    <option
      v-for='option in field.options' 
      :key='option' 
      :value='option'
    >{{ option }}</option>
    </select>
  </div>  
</template>

<script setup>

const props = defineProps({
  field: {
    type: Object,
    required: true,
  },
  modelValue: {
    type: [String, Number],
    default: ''
  },
  error: {
    type: Array,
    default: null
    },
})

const emit = defineEmits(['update:modelValue', 'validate'])

const handleChange = (event) => {
  emit('update:modelValue', event.target.value)
}

const handleBlur = (event) => {
  emit('validate', event.target.value)
}
</script>

<style scoped>

.select-placeholder {
  color: #999 !important;
}

.select:not(.select-placeholder) {
  color: #000 !important;
}

.select:focus {
  outline: none;
  border-color: #3acfcf;
  box-shadow: 0 0 0 2px rgba(58, 207, 207, 0.2);
}

.select-error {
  border-color: #6b21acff !important;
  background-color: rgba(107, 33, 172, 0.07) !important;
}

.select-error:focus {
  border-color: #6b21acff !important;
  box-shadow: 0 0 0 2px rgba(107, 33, 172, 0.2) !important;
}

.select {
  width: 100%;
  padding: 10px 12px;
  border: 1.8px solid #000000ff;
  border-radius: 0;
  font-size: 14px;
  background-color: #fff;
  cursor: pointer;
  transition: border-color 0.3s, background-color 0.3s;
  color: #000;
  box-sizing: border-box;
}

.select:focus {
  outline: none;
  border-color: #3acfcf;
  box-shadow: 0 0 0 2px rgba(58, 207, 207, 0.2);
}

.select option:first-child {
  color: #000 !important;
  background-color: #fff !important;
}

.select option:not(:first-child) {
  color: #000;
  background-color: #fff;
}

.select option {
  color: #000;
  background-color: #fff;
  padding: 8px;
}

.select option:disabled {
  color: #999 !important;
  font-style: italic;
}

.error-messages {
  margin-top: 5px;
}

.error-message {
  color: #6b21acff;
  font-size: 12px;
  margin-bottom: 2px;
}

.label {
  display: block;
  margin-bottom: 5px;
  font-weight: 500;
  color: #000000ff;
  font-size: 14px;
}

.required {
  color: #6b21acff;
  font-weight: bold;
}

</style>