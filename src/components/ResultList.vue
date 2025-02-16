<!-- src/components/ResultList.vue -->
<template>
  <div class="border p-4 rounded shadow">
    <h2 class="text-xl font-semibold mb-2 text-black">{{ title }}</h2>
    <p class="text-sm text-gray-600 mb-2">
      Total results: {{ totalHit }} | Time: {{ elapsed.toFixed(3) }} s
    </p>
    <div v-for="(result, index) in results" :key="index" class="mb-4">
      <a :href="result.url" target="_blank" class="text-blue-500 font-bold">
        {{ result.title }}
      </a>
      <!-- v-html renders the <b> tags -->
      <p class="text-sm text-black snippet" v-html="highlightSnippet(result.text)"></p>
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

// Utility: Escape regex special characters in the query term.
const escapeRegex = (str: string): string => {
  return str.replace(/[.*+?^${}()|[\]\\]/g, '\\$&')
}

const highlightSnippet = (text: string): string => {
  if (!text) return ''

  // Break the text into individual sentences using punctuation.
  let sentences = text.split(/(?<=[.!?])\s+/)
  sentences = sentences.map((s) => s.trim()).filter((s) => s.length > 0)

  // Split the query into individual terms.
  const queryTerms = props.query.split(/\s+/).filter((term) => term)

  let snippet = ''

  if (sentences.length > 1) {
    // Try to find the first sentence that contains any query term.
    const matchIndex = sentences.findIndex((sentence) =>
      queryTerms.some((term) => new RegExp(escapeRegex(term), 'i').test(sentence)),
    )

    if (matchIndex === -1) {
      // If no sentence contains the query term, fall back to the first two sentences.
      snippet = sentences.slice(0, 2).join(' ')
    } else {
      // Otherwise, select the matching sentence plus one sentence before and after (if available).
      const start = Math.max(matchIndex - 1, 0)
      const end = Math.min(matchIndex + 1, sentences.length - 1)
      snippet = sentences.slice(start, end + 1).join(' ')
    }
  } else {
    // Fallback: if sentence splitting doesn't work, take the first 300 characters.
    snippet = text.substring(0, 300)
  }

  // For each query term, wrap occurrences with <b> tags.
  queryTerms.forEach((term) => {
    const regex = new RegExp(`(${escapeRegex(term)})`, 'gi')
    snippet = snippet.replace(regex, '<b>$1</b>')
  })

  return snippet
}
</script>

<style scoped>
/* Limit snippet display to approximately 2 lines */
.snippet {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2; /* Change to 3 if you prefer 3 lines */
  overflow: hidden;
  line-height: 1.5;
}
</style>
