<template>
  <form @submit.prevent="submit">
    <div class="flex flex-col gap-4 mb-4">
      <div class="dark:text-gray-100 cursor-default">Email:</div>
      <app-input
        ref="email-input"
        type="email"
        placeholder="Введите email"
        with-error
        :error="formErrors.email"
        v-model="userForm.email"
        @focus="formErrors.email = ''"
      />
    </div>
    <div class="flex flex-col gap-4 mb-4">
      <div class="dark:text-gray-100 cursor-default">Пароль:</div>
      <app-input
        type="password"
        placeholder="Введите пароль"
        autocomplete="on"
        with-error
        :error="formErrors.password"
        v-model="userForm.password"
        @focus="formErrors.password = ''"
      />
    </div>
    <app-button type="submit">Войти</app-button>
  </form>
</template>

<script setup lang="ts">
import { reactive, ref, useTemplateRef } from 'vue'
import { useFocus } from '@vueuse/core'
import type { IUserData } from '@/types'
import { userSchema } from '@/types/validation'

useFocus(useTemplateRef<HTMLInputElement>('email-input'), { initialValue: true })

const emit = defineEmits<{
  (e: 'submit', form: IUserData): void
}>()
const { initialForm } = defineProps<{ initialForm: IUserData }>()

const userForm = reactive<IUserData>(initialForm)
const formErrors = ref({ email: '', password: '' })

const submit = () => {
  const { success, error } = userSchema.safeParse(userForm)

  if (success) {
    emit('submit', userForm)
  } else {
    const foundErrors = { email: '', password: '' }

    error.errors.forEach((zodErrorRecord) => {
      const key = zodErrorRecord.path[0] as keyof typeof foundErrors
      foundErrors[key] = zodErrorRecord.message
    })
    formErrors.value = foundErrors
  }
}
</script>
