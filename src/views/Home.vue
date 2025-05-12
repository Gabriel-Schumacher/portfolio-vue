<template>
  <div>
    <h1 class="text-4xl py-4">Products</h1>          
    <Categories :categories="categories" @categorySelected="handleCategorySelected" />
    <div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4 ">
      <div v-for="item in filteredItems" :key="item.id" class="product ">
        <Item :item="item" @add-to-cart="openAddToCartModal" />
      </div>
    </div>
    <div v-if="filteredItems.length === 0" class="text-center py-8 text-gray-500">
      No products found for the selected categories. Please select at least one category.
    </div>
  </div>
</template>

<script setup lang="ts">
import { inject } from 'vue';
import Categories from '../components/Categories.vue';
import Item from '../components/Item.vue';

// Inject the shared state and functions from App.vue
interface Item {
  id: number;
  title: string;
  description: string;
  price: number;
  image: string;
}

const filteredItems = inject<Item[]>('filteredItems') || [];
const categories = inject<string[]>('categories') || [];
const handleCategorySelected = inject<(...args: any[]) => any>('handleCategorySelected');
const openAddToCartModal = inject<(...args: any[]) => void>('openAddToCartModal');
</script>
