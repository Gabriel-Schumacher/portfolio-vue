<script setup lang="ts">
  import { ref, onMounted } from 'vue';
  import NavBar from './components/NavBar.vue';
  import Item from './components/Item.vue';
  import Categories from './components/Categories.vue';
  import AddToModal from './components/AddToModal.vue';
  import type { Item as ItemType, CartItem } from './types';

  let showModal = ref(false);
  let selectedItem = ref<ItemType | null>(null);

  let cart = ref(0);

  const data = ref<ItemType[] | null>(null);
  const categories = ref<string[]>([]);

  onMounted(async () => {
    try {
      const res = await fetch('https://fakestoreapi.com/products');
      data.value = await res.json();
      if (data.value) {
        categories.value = [...new Set(data.value.map((item: ItemType) => item.category))];
      }
    } catch (error) {
      console.error('Failed to fetch products:', error);
    }
  });
  
  // Function to handle add to cart from Item component
  const openAddToCartModal = (item: ItemType) => {
    selectedItem.value = item;
    showModal.value = true;
  };
  
  // Function to handle add to cart from modal
  const handleAddToCart = (itemWithQuantity: CartItem) => {
    // Here you would add the logic to add the item to the cart
    console.log('Item added to cart:', itemWithQuantity);
    // Increment the cart counter
    cart.value += itemWithQuantity.quantity;
  };
</script>

<template>
  <NavBar :cartNumber="cart" />

  <div class="bg-gray-100">
    <div class="container w-[90%] mx-auto">
      <h1 class="text-4xl py-4">Products</h1>          
      <Categories :categories="categories" />
      <div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4 ">
        <div v-for="item in data || []" :key="item.id" class="product">
          <Item :item="item" @add-to-cart="openAddToCartModal" />
        </div>
      </div>
    </div>    
  </div>
  
  <AddToModal 
    :showModal="showModal" 
    :item="selectedItem" 
    @close="showModal = false" 
    @add-to-cart="handleAddToCart" 
  />
</template>