<script setup lang="ts">
defineProps<{
  categories: string[]
}>();
</script>

<template>
  <div class="flex gap-2 flex-wrap mb-4">
    <button 
      v-for="category in categories" 
      :key="category" 
      class="bg-white px-3 py-1 rounded-full text-sm shadow hover:bg-gray-100"
    >
      {{ category }}
    </button>
  </div>
</template>