<script setup lang="ts">
import { inject, computed, ref } from 'vue';
import { useRouter } from 'vue-router';
import type { CartItem } from '../types';

const router = useRouter();
// Provide default empty array to avoid null/undefined issues
const cartItems = inject<CartItem[]>('cartItems', ref([]));
const removeFromCart = inject('handleRemoveCartItem', (id: number) => {
  console.warn('Remove from cart function not provided');
});

// Add update quantity function
const updateQuantity = inject('handleUpdateCartQuantity', (id: number, quantity: number) => {
  console.warn('Update quantity function not provided');
});

// Add clear cart function
const clearCart = inject('handleClearCart', () => {
  console.warn('Clear cart function not provided');
});

// Add show notification function
const showNotification = inject('handleShowNotification', (message: string) => {
  console.warn('Show notification function not provided');
});

const totalPrice = computed(() => {
  // Add a safeguard to ensure cartItems exists and is an array
  if (!cartItems || !Array.isArray(cartItems.value)) {
    return '0.00';
  }
  return cartItems.value.reduce((total, item) => 
    total + (item.price * item.quantity), 0).toFixed(2);
});

// Function to handle checkout process
const handleCheckout = () => {
  // Show thank you message
  showNotification('Thank you for your purchase!');
  
  // Clear the cart
  clearCart();
  
  // Redirect to shop page
  router.push('/');
};
</script>

<template>
  <div class="container mx-auto p-4 h-screen">
    <h1 class="text-2xl font-bold mb-4">Shopping Cart</h1>
    <div v-if="!cartItems || cartItems.length === 0" class="text-center">
      <p>Your cart is empty.</p>
    </div>
    <div v-else>
      <ul class="space-y-4">
        <li v-for="item in cartItems" :key="item.id" class="p-4 border-b">
          <div class="flex p-4 gap-2">
            <div>
                <img :src="item.image" alt="Product Image" class="w-16 h-16 object-cover mr-4">   
            </div>
            <div class="flex-grow">
                <h2 class="text-lg font-semibold">{{ item.title }}</h2>
                <div class="flex items-center mt-2">
                  <span class="mr-2">Quantity:</span>
                  <div class="flex items-center border rounded">
                    <button 
                      @click="updateQuantity(item.id, item.quantity - 1)" 
                      class="px-2 py-1 bg-gray-100 hover:bg-gray-200"
                      :disabled="item.quantity <= 1">−</button>
                    <span class="px-3">{{ item.quantity }}</span>
                    <button 
                      @click="updateQuantity(item.id, item.quantity + 1)" 
                      class="px-2 py-1 bg-gray-100 hover:bg-gray-200">+</button>
                  </div>
                </div>
                <p class="mt-1">Price: ${{ item.price.toFixed(2) }}</p>
                <p class="font-medium">Item Total: ${{ (item.price * item.quantity).toFixed(2) }}</p>
            </div>            
          </div>
          <button @click="removeFromCart(item.id)" class="text-red-500 hover:text-red-700 cursor-pointer">Remove</button>
        </li>
      </ul>
      <div class="mt-4">
        <h2 class="text-xl font-bold">Total: ${{ totalPrice }}</h2>
      </div>
      <div class="mt-4">
        <button @click="handleCheckout" class="btn bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700">
          Confirm Order and Checkout
        </button>
      </div>
    </div>
  </div>
</template>

