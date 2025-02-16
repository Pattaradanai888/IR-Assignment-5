<!-- src/views/SearchResult.vue -->
<template>
  <div class="container mx-auto p-4">
    <!-- Persistent header similar to Google search results -->
    <header class="flex items-center py-4 shadow-md bg-white">
      <img
        src="@/assets/google-logo.png"
        alt="Google"
        class="w-24 mr-4 cursor-pointer"
        @click="router.push({ path: '/' })"
      />
      <SearchBar v-model="query" @search="handleSearch" />
    </header>
    <div class="py-2">
      <h1 class="text-2xl font-bold text-black">
        Search Results for "<span class="text-blue-600">{{ query }}</span
        >"
      </h1>
    </div>
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <!-- Left-hand side: Elasticsearch BM25 + PageRank -->
      <ResultList
        title="Elasticsearch BM25 + PageRank"
        :totalHit="esResults.total_hit"
        :elapsed="esResults.elapse"
        :results="esResults.results"
        :query="query"
      />
      <!-- Right-hand side: Custom TF-IDF + PageRank -->
      <ResultList
        title="Custom TF-IDF + PageRank"
        :totalHit="customResults.total_hit"
        :elapsed="customResults.elapse"
        :results="customResults.results"
        :query="query"
      />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import axios from 'axios'
import ResultList from '@/components/ResultList.vue'
import SearchBar from '@/components/SearchBar.vue'

interface SearchResult {
  title: string
  url: string
  text: string
  score: number
}

interface BackendResponse {
  status: string
  total_hit: number
  results: SearchResult[]
  elapse: number
}

const route = useRoute()
const router = useRouter()
const query = ref<string>((route.query.q as string) || '')

const esResults = ref<BackendResponse>({
  status: '',
  total_hit: 0,
  results: [],
  elapse: 0,
})

const customResults = ref<BackendResponse>({
  status: '',
  total_hit: 0,
  results: [],
  elapse: 0,
})

const performSearch = async () => {
  if (query.value.trim() === '') return

  try {
    const esResponse = await axios.get<BackendResponse>('http://localhost:5000/search_es_pr', {
      params: { query: query.value },
    })
    esResults.value = esResponse.data
  } catch (error) {
    console.error('Error fetching ES results:', error)
  }

  try {
    const customResponse = await axios.get<BackendResponse>(
      'http://localhost:5000/search_manual_pr',
      { params: { query: query.value } },
    )
    customResults.value = customResponse.data
  } catch (error) {
    console.error('Error fetching custom results:', error)
  }
}

onMounted(() => {
  if (query.value.trim() !== '') {
    performSearch()
  }
})

const handleSearch = () => {
  // Update URL query parameter and perform a new search.
  router.push({ path: '/search', query: { q: query.value } })
  performSearch()
}
</script>
