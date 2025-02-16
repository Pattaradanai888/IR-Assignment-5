<!-- src/components/ResultList.vue -->
<template>
  <div class="border p-4 rounded shadow">
    <h2 class="text-xl font-semibold mb-2">{{ title }}</h2>
    <p class="text-sm text-gray-600 mb-2">
      Total results: {{ totalHit }} | Time: {{ elapsed.toFixed(3) }} s
    </p>
    <div v-for="(result, index) in results" :key="index" class="mb-4">
      <a :href="result.url" target="_blank" class="text-blue-500 font-bold">
        {{ result.title }}
      </a>
      <p class="text-sm" v-html="highlightSnippet(result.text)"></p>
      <p class="text-xs text-gray-500">Score: {{ result.score.toFixed(7) }}</p>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { defineProps } from 'vue'

interface SearchResult {
  title: string
  url: string
  text: string
  score: number
}

const props = defineProps<{
  title: string
  totalHit: number
  elapsed: number
  results: SearchResult[]
  query: string
}>()

const highlightSnippet = (text: string): string => {
  if (!text) return ''
  // Split text into sentences (using punctuation as delimiter)
  const sentences = text.split(/(?<=[.?!])\s+/)
  const queryTerms = props.query.split(/\s+/).filter((term) => term)
  // Filter sentences that include any query term (case-insensitive)
  let matchedSentences = sentences.filter((sentence) =>
    queryTerms.some((term) => new RegExp(term, 'i').test(sentence)),
  )
  // If no sentence contains the query term, take the first two sentences.
  if (matchedSentences.length === 0) {
    matchedSentences = sentences.slice(0, 2)
  } else if (matchedSentences.length > 2) {
    matchedSentences = matchedSentences.slice(0, 2)
  }
  let snippet = matchedSentences.join(' ')
  // Highlight each occurrence of the query term by wrapping it in <b> tags.
  queryTerms.forEach((term) => {
    const regex = new RegExp(`(${term})`, 'gi')
    snippet = snippet.replace(regex, '<b>$1</b>')
  })
  return snippet
}
</script>

<style scoped>
/* Limit snippet display to approximately two lines */
p {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
