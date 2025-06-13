<template>
  <div class="max-w-lg mx-auto py-10">
    <h1 class="text-2xl font-bold mb-4">User Profile</h1>
    <div v-if="pending">Loading...</div>
    <div v-else-if="error">Error: {{ error.message }}</div>
    <div v-else>
        <div class="space-y-4">
            <p><strong>Name:</strong> {{ data?.name }}</p>
            <p><strong>Email:</strong> {{ data?.email }}</p>
      
            <UButton @click="logout">Logout</UButton>
        </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const router = useRouter()
interface UserResponse {
  name: string
  email: string
}

const token = useCookie('token')
if(!token.value) { 
    router.push('/login')
}
const { data, pending, error } = await useFetch<UserResponse>('http://localhost:3500/auth/getProfile', {
  headers: {
    Authorization: `Bearer ${token.value}`
  }
})


function logout() {
  useCookie('token').value = null

  router.push('/login')
}

</script>