<script setup>
import { ref, computed } from "vue";
import TextField from "./inputs/TextField.vue";
import SelectField from "./inputs/SelectField.vue";
import CheckboxField from "./inputs/CheckboxField.vue";

const props = defineProps({
  schema: Object,
  modelValue: Object,
});

const emit = defineEmits(["update:modelValue", "submit"]);

const errors = ref({});

const formValid = ref(true);

const submitInput = ref('');

const formData = computed({
  get() {
    return props.modelValue || {};
  },
  set(value) {
    emit("update:modelValue", value);
  },
});

const updateField = (fieldName, value) => {
  formData.value[fieldName] = value;
  emit("update:modelValue", { ...formData.value });

  if (errors.value[fieldName]) {
    delete errors.value[fieldName];
  }
};

const validateField = (fieldName, value) => {
  const field = props.schema.fields.find((f) => f.model === fieldName);
  const fieldErrors = [];

  if (field.required) {
    if (field.type === "checkbox") {
      if (!value) {
        fieldErrors.push("Необходимо согласие с условиями");
      }
    } else {
      if (!value || (typeof value === "string" && !value.trim())) {
        fieldErrors.push("Это поле обязательное для заполнения");
      }
    }
  }

  if (value && field.minLength) {
    if (value.length < field.minLength) {
      fieldErrors.push(`Минимум ${field.minLength} символов`);
    }
  }

  if (fieldErrors.length > 0) {
    errors.value[fieldName] = fieldErrors;
  } else {
    delete errors.value[fieldName];
  }
};

const validateForm = () => {
  errors.value = {};

  props.schema.fields.forEach((field) => {
    const value = formData.value[field.model];
    validateField(field.model, value);
  });

  return Object.keys(errors.value).length === 0;
};

const submitForm = () => {
  const isValid = validateForm();
  formValid.value = isValid;

  if (isValid) {
    submitInput.value = 'Данные приняты';
    emit("submit", { ...formData.value });

    setTimeout(() => {
      clearForm();
    }, 1500);
  } else {
    submitInput.value = 'Не все обязательные поля заполнены корректно';
  }
};
</script>

<template>
  <div class="form-container">
    <div v-for="field in schema.fields" :key="field.model" class="field-item">
      <TextField
        v-if="!['select', 'checkbox'].includes(field.type)"
        :field="field"
        :modelValue="formData[field.model]"
        :hasError="!!errors[field.model]"
        :error="errors[field.model]"
        @update:modelValue="updateField(field.model, $event)"
        @validate="validateField(field.model, $event)"
      />
      <SelectField
        v-if="field.type === 'select'"
        :field="field"
        :modelValue="formData[field.model]"
        :hasError="!!errors[field.model]"
        :error="errors[field.model]"
        @update:modelValue="updateField(field.model, $event)"
        @validate="validateField(field.model, $event)"
      />
      <CheckboxField
        v-if="field.type === 'checkbox'"
        :field="field"
        :modelValue="formData[field.model]"
        :hasError="!!errors[field.model]"
        :error="errors[field.model]"
        @update:modelValue="updateField(field.model, $event)"
        @validate="validateField(field.model, $event)"
      />
    </div>
    <button
    type="button"
    :class="['primary', { invalid: formValid === false }]"
    @click="submitForm"
  >
    Отправить
  </button>
  <div v-if='submitInput' class='submit-error'>
    {{ submitInput }}
  </div>
  </div>
</template>

<style scoped>
.form-generator {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.form-container {
  max-width: 600px;
  width: 100%;
  margin: 0 auto;
  padding: 20px;
  box-sizing: border-box;
}

.field-item {
  display: flex;
  flex-direction: column;
  margin-bottom: 14px;
}

.field-item label {
  font-size: 14px;
  margin-bottom: 6px;
  color: #111;
}

.field-item input:focus,
.field-item select:focus {
  outline: none;
  border-color: #3acfcf;
}

.submit-error {
  margin-top: 8px;
  padding: 12px;
  background-color: rgba(107, 33, 172, 0.07);
  border: 1.8px solid #6b21acff;
  border-radius: 0;
  color: #6b21acff;
  font-size: 14px;
  font-weight: 500;
}

button[type="submit"] {
  width: 100%;
  padding: 14px;
  background: #3acfcf;
  border: 1px solid #111;
  border-radius: 4px;
  cursor: pointer;
  font-weight: 600;
  font-size: 16px;
  transition: 0.2s;
}

button[type="submit"]:hover {
  background: #2fbabb;
}

button.primary {
  background: #3acfcf;
  border: 1.8px solid #111;
  padding: 16px;
  border-radius: 0;
  font-size: 18px;
  
  width: auto;
  min-width: 150px;
  margin: 20px 0 0 auto;
  display: block;
}
</style>
