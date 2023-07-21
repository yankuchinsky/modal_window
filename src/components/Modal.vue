<script setup lang="ts">
import { reactive, computed } from 'vue'
import useValidate from '@vuelidate/core'
import { required, maxLength, email, helpers } from '@vuelidate/validators'

const phoneValidator = (value: string) =>
  !!value.match(
    /^(\+7|7|8)?[\s\-]?\(?[489][0-9]{2}\)?[\s\-]?[0-9]{3}[\s\-]?[0-9]{2}[\s\-]?[0-9]{2}$/g
  )

type TFields = {
  name: string
  lastName: string
  email: string
  phone: string
  message: string
}

const fields: TFields = {
  name: '',
  lastName: '',
  email: '',
  phone: '',
  message: ''
}

const formData = reactive({ ...fields })

const rules = computed(() => ({
  name: { required, maxLength: maxLength(100) },
  lastName: { maxLength: maxLength(100) },
  email: { required, email },
  phone: { required, pphoneValidator: helpers.withMessage('Phone not valid', phoneValidator) },
  message: { maxLength: maxLength(500) }
}))

const v$ = useValidate(rules, formData)

const validate = async (value: keyof TFields) => {
  await v$.value[value].$validate()
}

const submit = async () => {
  const result = await v$.value.$validate()
  if (result) {
    alert('successfully submited')
  } else {
    alert('error')
  }
}
</script>
<template>
  <div class="modal-window">
    <div class="content">
      <div class="close" @click="$emit('close')">x</div>
      <div class="form">
        <div class="input-wrapper">
          <label>
            Name*
            <input v-model="formData.name" @blur="validate('name')" />
            <div class="error-label" v-for="error in v$.name.$errors" :key="error.$uid">
              {{ error.$message }}
            </div>
          </label>
        </div>
        <div class="input-wrapper">
          <label>
            Last Name*
            <input v-model="formData.lastName" @blur="validate('lastName')"/>
            <div class="error-label" v-for="error in v$.lastName.$errors" :key="error.$uid">
              {{ error.$message }}
            </div>
          </label>
        </div>
        <div class="input-wrapper">
          <label>
            Phone*
            <input v-model="formData.phone" @blur="validate('phone')"/>
            <div class="error-label" v-for="error in v$.phone.$errors" :key="error.$uid">
              {{ error.$message }}
            </div>
          </label>
        </div>
        <div class="input-wrapper">
          <label>
            Email*
            <input v-model="formData.email" @blur="validate('email')"/>
            <div class="error-label" v-for="error in v$.email.$errors" :key="error.$uid">
              {{ error.$message }}
            </div>
          </label>
        </div>
        <div class="input-wrapper">
          <label>
            Message
            <textarea v-model="formData.message" @blur="validate('message')"/>
            <div class="error-label" v-for="error in v$.message.$errors" :key="error.$uid">
              {{ error.$message }}
            </div>
          </label>
        </div>
        <div class="input-wrapper">
          <button @click="submit">Submit</button>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.modal-window {
  position: absolute;
  top: 10px;
  right: 10px;
  border: 1px solid #000;
  border-radius: 4px;
  display: flex;
  align-items: center;
  flex-direction: row;
  justify-content: center;
  position: relative;
  padding: 20px;
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  cursor: pointer;
}

.form {
  display: flex;
  flex-direction: column;
}

.input-wrapper {
  display: flex;
  justify-content: flex-end;
  padding: 4px 10px;
}

.input-wrapper input {
  max-width: 250px;
}

.input-error {
  outline: 1px solid red;
}

.error-label {
  color: red;
}
</style>
