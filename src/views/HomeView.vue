<!-- src/views/HomeView.vue -->
<template>
  <main class="bg-gray-100">
    <header class="flex justify-end p-4 text-sm text-gray-700">
      <nav>
        <ul class="flex space-x-4">
          <li><a href="#" class="hover:underline">Gmail</a></li>
          <li><a href="#" class="hover:underline">Images</a></li>
        </ul>
      </nav>
    </header>
    <div class="flex flex-col items-center justify-center flex-grow">
      <!-- Google logo (ensure you have a logo image in src/assets) -->
      <img src="@/assets/google-logo.png" alt="Google" class="mb-8 w-2xs" />
      <div class="w-full max-w-md">
        <form @submit.prevent="submitSearch" class="flex flex-col items-center">
          <div class="relative w-full mb-4">
            <span class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-500">
              <i class="fas fa-search"></i>
            </span>
            <input
              v-model="query"
              type="text"
              placeholder="Search Google or type a URL"
              class="input input-bordered w-full pl-10 pr-10 py-2 rounded-full border-gray-300 bg-white text-black shadow-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
            />

            <span class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-500">
              <i class="fas fa-microphone"></i>
            </span>
          </div>
          <div class="flex space-x-2">
            <button
              type="submit"
              class="btn btn-primary px-4 py-2 rounded bg-gray-100 text-gray-800 hover:bg-gray-200 shadow-md"
            >
              Google Search
            </button>
            <button
              type="button"
              class="btn btn-secondary px-4 py-2 rounded bg-gray-100 text-gray-800 hover:bg-gray-200 shadow-md"
            >
              I'm Feeling Lucky
            </button>
          </div>
        </form>
      </div>
    </div>
  </main>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const query = ref<string>('')

const submitSearch = () => {
  if (query.value.trim() !== '') {
    // Redirect to the search results page with the query as a parameter.
    router.push(`/search?q=${encodeURIComponent(query.value)}`)
  }
}
</script>
