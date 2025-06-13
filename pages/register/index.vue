<script setup lang="ts">
import * as v from 'valibot'
import type { FormSubmitEvent } from '@nuxt/ui'

const schema = v.object({
    name: v.pipe(v.string(), v.minLength(1, 'cannot be null')),
    email: v.pipe(v.string(), v.email('Invalid email')),
    password: v.pipe(v.string(), v.minLength(6, 'Must be at least 6 characters')),
    confirmPassword: v.pipe(v.string(), v.minLength(6, 'Must be at least 6 characters'))
})

type Schema = v.InferOutput<typeof schema>

const state = reactive({
    name: '',
    email: '',
    password: '',
    confirmPassword: ''
})

const toast = useToast()
async function onSubmit(event: FormSubmitEvent<Schema>) {
    if (event.data.password !== event.data.confirmPassword) {
        toast.add({ title: 'Error', description: 'Password not match', color: 'error' })
    } else {
        try {
            const response = await $fetch('http://localhost:3500/auth/register', {
                method: 'POST',
                body: event.data,
                headers: {
                    'Content-Type': 'application/json'
                }
            })
            console.log(response)
            toast.add({ title: 'Success', description: 'The form has been submitted.', color: 'success' })
        } catch (err) {
            console.error('API Error:', err)
            toast.add({ title: 'Error', description: 'Something went wrong', color: 'error' })
        }
    }
}
</script>


<template>
    <div class="max-w-screen-sm mx-auto py-10">
        <UForm :state="state" :schema="schema" class="space-y-4" @submit="onSubmit">
            <h1 class="text-2xl font-bold text-center">Form Register</h1>
            <div class="flex justify-center space-x-4">
                <UFormField label="Name" name="name" size="lg">
                    <UInput v-model="state.name" />
                </UFormField>
                <UFormField label="Email" name="email" size="lg">
                    <UInput v-model="state.email" />
                </UFormField>
            </div>

            <div class="flex justify-center space-x-4">
                <UFormField label="Password" name="password" size="lg">
                    <UInput v-model="state.password" type="password" />
                </UFormField>
                <UFormField label="Confirm Password" name="confirmPassword" size="lg">
                    <UInput v-model="state.confirmPassword" type="password" />
                </UFormField>
            </div>
            <div class="flex justify-center">
                <UButton type="submit" size="lg">
                    Submit
                </UButton>
            </div>
        </UForm>

        <p class="text-center mt-10">if already have account : <a href="/login" class="text-blue-500 underline hover:text-white">login here !</a></p>
    </div>
</template>