<script setup lang="ts">
import { ref, computed } from 'vue';
import { useRouter } from 'vue-router';
import type { CartItem } from '../types';

const props = defineProps<{
  cartNumber: number;
  cartItems: CartItem[];
}>();

const router = useRouter();
const showDropdown = ref(false);

const toggleDropdown = () => {
  showDropdown.value = !showDropdown.value;
};

const closeDropdown = () => {
  showDropdown.value = false;
};

const navigateToCart = () => {
  router.push('/cart');
  closeDropdown();
};

const calculateTotal = computed(() => {
  const subtotal = props.cartItems.reduce(
    (total, item) => total + item.price * item.quantity, 
    0
  );
  const tax = subtotal * 0.0725; // 7.25% tax rate
  return {
    subtotal: subtotal.toFixed(2),
    tax: tax.toFixed(2),
    total: (subtotal + tax).toFixed(2)
  };
});
</script>

<template>
  <nav class="bg-gray-800 text-white p-4">
    <div class="container mx-auto flex justify-between items-center">
      <router-link to="/" class="text-xl font-bold">Shop</router-link>
      
      <div class="relative">
        <button 
          @click="toggleDropdown" 
          class="flex items-center space-x-1 px-4 py-2 rounded hover:bg-gray-700"
        >
          <span>Cart</span>
          <span class="bg-red-500 rounded-full h-5 w-5 flex items-center justify-center text-xs">{{ cartNumber }}</span>
        </button>
        
        <!-- Cart Dropdown -->
        <div 
          v-if="showDropdown" 
          class="absolute right-0 mt-2 w-80 bg-white rounded-md shadow-lg z-10 text-gray-800"
        >
          <div class="p-4">
            <h3 class="text-lg font-medium border-b pb-2">Your Cart</h3>
            
            <div v-if="cartItems.length === 0" class="py-4 text-center text-gray-500">
              Your cart is empty
            </div>
            
            <div v-else>
              <div class="max-h-60 overflow-y-auto py-2">
                <div v-for="item in cartItems" :key="item.id" class="flex items-center py-2 border-b">
                  <img :src="item.image" alt="item.title" class="h-10 w-10 object-cover mr-2">
                  <div class="flex-grow">
                    <p class="text-sm truncate">{{ item.title }}</p>
                    <div class="flex justify-between text-xs">
                      <span>${{ item.price }} x {{ item.quantity }}</span>
                      <span>${{ (item.price * item.quantity).toFixed(2) }}</span>
                    </div>
                  </div>
                </div>
              </div>
              
              <div class="mt-4 space-y-1">
                <div class="flex justify-between text-sm">
                  <span>Subtotal:</span>
                  <span>${{ calculateTotal.subtotal }}</span>
                </div>
                <div class="flex justify-between text-sm">
                  <span>Tax (7.25%):</span>
                  <span>${{ calculateTotal.tax }}</span>
                </div>
                <div class="flex justify-between font-bold border-t pt-1">
                  <span>Total:</span>
                  <span>${{ calculateTotal.total }}</span>
                </div>
              </div>
              
              <div class="mt-4 flex justify-between">
                <button 
                  @click="navigateToCart" 
                  class="bg-blue-600 text-white px-3 py-1 rounded text-sm hover:bg-blue-700"
                >
                  Edit Cart
                </button>
                <button 
                  class="bg-green-600 text-white px-3 py-1 rounded text-sm hover:bg-green-700"
                >
                  Checkout
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </nav>
</template>

<style scoped>
/* Add any additional styles here */
</style>