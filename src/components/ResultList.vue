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

// Utility to escape special characters in regex.
const escapeRegExp = (string: string): string => {
  return string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&')
}

const highlightSnippet = (text: string): string => {
  if (!text) return ''

  // Split text into sentences using punctuation as delimiter.
  const sentences = text.split(/(?<=[.?!])\s+/)
  const queryTerms = props.query.split(/\s+/).filter((term) => term)

  // Find the index of the first sentence that contains any query term.
  const index = sentences.findIndex((sentence) =>
    queryTerms.some((term) => new RegExp(escapeRegExp(term), 'i').test(sentence)),
  )

  let snippetSentences: string[]

  if (index === -1) {
    // If no sentence contains the query term, default to the first two sentences.
    snippetSentences = sentences.slice(0, 2)
  } else {
    // Include the sentence containing the query term plus one sentence before and after (if available).
    const start = index > 0 ? index - 1 : index
    const end = index < sentences.length - 1 ? index + 1 : index
    snippetSentences = sentences.slice(start, end + 1)
  }

  let snippet = snippetSentences.join(' ')

  // Highlight each occurrence of the query term.
  queryTerms.forEach((term) => {
    const escapedTerm = escapeRegExp(term)
    const regex = new RegExp(`(${escapedTerm})`, 'gi')
    snippet = snippet.replace(regex, '<span class="highlight">$1</span>')
  })

  return snippet
}
</script>

<style scoped>
.snippet {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2; /* Limit to 2 lines */
  overflow: hidden;
  line-height: 1.5;
  width: 100%;
}

/* Style for the highlighted text */
.highlight {
  background-color: yellow;
  font-weight: bold;
}
</style>
