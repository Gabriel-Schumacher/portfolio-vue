<script setup lang="ts">
import { ref, onMounted } from 'vue';

const props = defineProps<{
  categories: string[]
}>();

const emit = defineEmits(['categorySelected']);

// Track selected categories in a Set for efficient lookup
const selectedCategories = ref<Set<string>>(new Set());

// Initialize with all categories selected
onMounted(() => {
  props.categories.forEach(category => {
    selectedCategories.value.add(category);
  });
  // Emit initial selection
  emitSelectedCategories();
});

// Toggle category selection
const toggleCategory = (category: string) => {
  if (selectedCategories.value.has(category)) {
    selectedCategories.value.delete(category);
  } else {
    selectedCategories.value.add(category);
  }
  emitSelectedCategories();
};

// Emit the current selection
const emitSelectedCategories = () => {
  emit('categorySelected', Array.from(selectedCategories.value));
};
</script>

<template>
  <div class="flex gap-2 flex-wrap mb-4">
    <button 
      v-for="category in categories" 
      :key="category" 
      :class="[
        'px-3 py-1 rounded-full text-sm shadow cursor-pointer transition-colors',
        selectedCategories.has(category) 
          ? 'bg-primary text-white' 
          : 'bg-white hover:bg-gray-100'
      ]"
      @click="toggleCategory(category)"
    >
      {{ category }}
    </button>
  </div>
</template>