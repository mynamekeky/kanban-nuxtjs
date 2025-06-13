<script setup lang="ts">
import * as v from 'valibot'
import type { FormSubmitEvent } from '@nuxt/ui'

interface LoginResponse {
  accessToken: string
}

const schema = v.object({
    email: v.pipe(v.string(), v.email('Invalid email')),
    password: v.pipe(v.string(), v.minLength(6, 'Must be at least 6 characters'))
})

type Schema = v.InferOutput<typeof schema>

const state = reactive({
    email: '',
    password: '',
})

const router = useRouter()
const toast = useToast()
async function onSubmit(event: FormSubmitEvent<Schema>) {
        try {
            const response = await $fetch<LoginResponse>('http://localhost:3500/auth/login', {
                method: 'POST',
                body: event.data,
                headers: {
                    'Content-Type': 'application/json'
                }
            })
            if (response) { 
                useCookie('token').value = response.accessToken
                toast.add({ title: 'Success', description: 'The form has been submitted.', color: 'success' })
                router.push('/')
            }
        } catch (err) {
            console.error('API Error:', err)
            toast.add({ title: 'Error', description: 'Something went wrong', color: 'error' })
        }
}
</script>


<template>
    <div class="max-w-screen-sm mx-auto py-10">
        <UForm :state="state" :schema="schema" class="space-y-4" @submit="onSubmit">
            <h1 class="text-2xl font-bold text-center">Form Register</h1>
            <div class="flex justify-center">
              <UFormField label="Email" name="email" size="lg">
                  <UInput v-model="state.email" />
              </UFormField>
            </div>
      

            <div class="flex justify-center">
                <UFormField label="Password" name="password" size="lg">
                    <UInput v-model="state.password" type="password" />
                </UFormField>
            </div>
         
            <div class="flex justify-center">
                <UButton type="submit" size="lg">
                    Submit
                </UButton>
            </div>
        </UForm>
        <p class="text-center mt-10">if not have account : <a href="/register" class="text-blue-500 underline hover:text-white">register here !</a></p>
    </div>
</template>