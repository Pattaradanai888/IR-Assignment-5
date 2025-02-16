<template>
  <div class="relative w-full max-w-lg">
    <span class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-500">
      <i class="fas fa-search"></i>
    </span>
    <input
      type="text"
      :value="modelValue"
      @input="$emit('update:modelValue', ($event.target as HTMLInputElement)?.value)"
      @keyup.enter="handleSearch"
      placeholder="Search Google or type a URL"
      class="input input-bordered w-full pl-10 pr-10 py-2 rounded-full border-gray-300 bg-white text-black focus:outline-none focus:ring-2 focus:ring-blue-500"
    />

    <span class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-500">
      <i class="fas fa-microphone"></i>
    </span>
  </div>
</template>

<script lang="ts" setup>
const props = defineProps<{
  modelValue: string
}>()

const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void
  (e: 'search'): void
}>()

const handleSearch = () => {
  emit('search')
}
</script>
