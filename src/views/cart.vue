<script setup lang="ts">
import { inject, computed, ref } from 'vue';
import type { CartItem } from '../types';

// Provide default empty array to avoid null/undefined issues
const cartItems = inject<CartItem[]>('cartItems', ref([]));
const removeFromCart = inject('handleRemoveCartItem', (id: number) => {
  console.warn('Remove from cart function not provided');
});

const totalPrice = computed(() => {
  // Add a safeguard to ensure cartItems exists and is an array
  if (!cartItems || !Array.isArray(cartItems)) {
    return '0.00';
  }
  return cartItems.reduce((total, item) => 
    total + (item.price * item.quantity), 0).toFixed(2);
});
</script>

<template>
  <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Shopping Cart</h1>
    <div v-if="!cartItems || cartItems.length === 0" class="text-center">
      <p>Your cart is empty.</p>
    </div>
    <div v-else>
      <ul class="space-y-4">
        <li v-for="item in cartItems" :key="item.id" class="p-4 border-b">
          <div  class="flex p-4">
            <div>
                <img :src="item.image" alt="Product Image" class="w-16 h-16 object-cover mr-4">   
            </div>
            <div>
                <h2 class="text-lg font-semibold">{{ item.title }}</h2>
                <p>Quantity: {{ item.quantity }}</p>
                <p>Price: ${{ item.price.toFixed(2) }}</p>
            </div>            
          </div>
          <button @click="removeFromCart(item.id)" class="text-red-500 hover:text-red-700 cursor-pointer">Remove</button>
        </li>
      </ul>
      <div class="mt-4">
        <h2 class="text-xl font-bold">Total: ${{ totalPrice }}</h2>
      </div>
    </div>
  </div>
</template>

