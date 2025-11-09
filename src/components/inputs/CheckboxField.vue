<template>
  <div class="checkbox-input">
    <label :for="field.model" class="checkbox-label">
      <input
        :id="field.model"
        type="checkbox"
        :checked="modelValue"
        :class="['checkbox', { 'checkbox-error': hasError }]"
        @change="handleChange"
        @blur="handleBlur"
      />
      <span class="checkmark"></span>
      <span class="label-text">
        {{ field.label }}
        <span v-if="field.required" class="required">*</span>
      </span>
    </label>
  </div>
</template>

<script setup>
const props = defineProps({
  field: {
    type: Object,
    required: true,
  },
  modelValue: {
    type: Boolean,
    default: false,
  },
  error: {
    type: Array,
    default: null,
  },
  hasError: {
   type: Boolean,
   default: false,
  },
});

const emit = defineEmits(["update:modelValue", "validate"]);

const handleChange = (event) => {
  emit("update:modelValue", event.target.checked);
};

const handleBlur = (event) => {
  emit("validate", event.target.checked);
};
</script>

<style scoped>
.checkbox-input {
  margin-bottom: 5px;
}

.checkbox-label {
  display: flex;
  align-items: center;
  cursor: pointer;
  font-size: 14px;
  color: #000000ff;
  font-weight: 500;
  position: relative;
}

/* Скрываем дефолтный чекбокс, берём кастомный */
.checkbox {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

.checkmark {
  height: 20px;
  width: 20px;
  background-color: #fff;
  border: 1.8px solid #000000ff;
  border-radius: 0;
  margin-right: 10px;
  position: relative;
  transition: border-color 0.3s, background-color 0.3s;
  box-sizing: border-box;
  flex-shrink: 0;
}

.label-text {
  display: flex;
  align-items: center;
}

.checkmark:after {
  content: "";
  position: absolute;
  display: none;
  left: 5px;
  top: 2px;
  width: 6px;
  height: 10px;
  border: solid #000;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
}

.checkbox:checked ~ .checkmark:after {
  display: block;
}

.checkbox:focus ~ .checkmark {
  border-color: #3acfcf;
  box-shadow: 0 0 0 2px rgba(58, 207, 207, 0.2);
}

.checkbox-error ~ .checkmark {
  border-color: #6b21acff !important;
  background-color: rgba(107, 33, 172, 0.07) !important;
}

.checkbox-error:focus ~ .checkmark {
  border-color: #6b21acff !important;
  box-shadow: 0 0 0 2px rgba(107, 33, 172, 0.2) !important;
}

/* Звездочка
.required {
  color: #6b21acff;
  font-weight: bold;
  margin-left: 4px;
}
*/

.error-messages {
  margin-top: 5px;
}

.error-message {
  color: #6b21acff;
  font-size: 12px;
  margin-bottom: 2px;
}
</style>
