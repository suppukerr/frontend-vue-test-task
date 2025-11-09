<template>
  <div id='app'>
    <div class='json-section'>
        <p>JSON-схема</p>

        <div class='schema-editor'>
          <textarea
            id='schemaInput'
            v-model='customSchemaText'
            class='schema-textarea'
            placeholder='Введите JSON-схему здесь...'
            rows='12'
            @input='parseCustomSchema'
          ></textarea>

          <div v-if='schemaError' class='schema-error'>
            Ошибка в JSON
          </div>
        </div>

        <div v-if='customSchema' class='form-wrapper'>
          <h3>Автоформа</h3>
          <FormGenerator 
            :schema='customSchema' 
            v-model='customFormData'
            @submit='handleCustomSubmit'
            @validate='handleValidate'
          />
        </div>
      </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from "vue";
import FormGenerator from "./components/FormGenerator.vue";

const defaultSchema = {
  fields: [
    { type: "text", label: "Имя", model: "name", required: true },
    { type: "email", label: "Email", model: "email", required: true },
    {
      type: "password",
      label: "Пароль",
      model: "password",
      required: true,
      minLength: 6,
    },
    {
      type: "select",
      label: "Роль",
      model: "role",
      options: ["Админ", "Пользователь"],
      required: true,
    },
    { type: "checkbox", label: "Согласен с условиями", model: "terms", required: true },
  ],
};
const customSchemaText = ref(JSON.stringify(defaultSchema, null, 2));

const customSchema = ref(defaultSchema);

const schemaError = ref(null);

const parseCustomSchema = () => {
  if (!customSchemaText.value.trim()) {
    customSchema.value = null;
    schemaError.value = null;
    return;
  }

  try {
    const parsed = JSON.parse(customSchemaText.value)
    
    if (parsed && parsed.fields && Array.isArray(parsed.fields)) {
      customSchema.value = parsed
      schemaError.value = null
    } else {
      throw new Error('Схема должна содержать массив "fields"')
    }
  } catch (err) {
    customSchema.value = null
    schemaError.value = err.message
  }
};

const finalSchema = computed(() =>
  customSchema.value || defaultSchema
);

const formData = ref({});
const customFormData = ref({});

const handleCustomSubmit = (data) => {
  console.log('Custom form submitted:', data);
};

const handleValidate = (result) => {
  console.log('Field validated:', result);
};

</script>

<style scoped>
#app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.json-section {
  margin-bottom: 30px;
  width: 100%;
  max-width: 800px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.json-section p {
  font-size: 18px;
  font-weight: 400;
  margin-bottom: 16px;
  color: #000;
  text-align: center;
}

.schema-editor {
  margin-bottom: 30px;
  width: 100%;
  max-width: 600px;
}

.schema-textarea {
  width: 100%;
  min-height: 300px;
  padding: 10px 12px;
  border: 1.8px solid #000000ff;
  border-radius: 0;
  font-size: 14px;
  font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
  line-height: 1.4;
  resize: vertical;
  transition: border-color 0.3s, background-color 0.3s;
  box-sizing: border-box;
  background-color: #fff;
  color: #000;
}

.schema-textarea:focus {
  outline: none;
  border-color: #3acfcf;
  box-shadow: 0 0 0 2px rgba(58, 207, 207, 0.2);
}

.schema-textarea::placeholder {
  color: #000000cd !important;
  opacity: 1;
}

.schema-textarea::-webkit-input-placeholder {
  color: #000000cd !important;
}

.schema-textarea::-moz-placeholder {
  color: #000000cd !important;
  opacity: 1;
}

.schema-textarea:-ms-input-placeholder {
  color: #000000cd !important;
}

.schema-error {
  margin-top: 8px;
  padding: 12px;
  background-color: rgba(107, 33, 172, 0.07);
  border: 1.8px solid #6b21acff;
  border-radius: 0;
  color: #6b21acff;
  font-size: 14px;
  font-weight: 500;
}

.form-wrapper {
  margin-top: 40px;
  padding-top: 30px;
  width: 80%;
  max-width: 500px;
  border-top: 1.8px solid #000;
}

.form-wrapper h3 {
  font-size: 20px;
  font-weight: 400;
  text-align: center;
  margin-bottom: 20px;
  color: #000;
}
</style>
