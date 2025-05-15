<script setup lang="ts">
  import { ref, onMounted, computed, provide, reactive } from 'vue';
  import NavBar from './components/NavBar.vue';
  import AddToModal from './components/AddToModal.vue';
  import type { Item as ItemType, CartItem } from './types';

  let showModal = ref(false);
  let selectedItem = ref<ItemType | null>(null);
  let cart = ref(0);
  let cartItems = ref<CartItem[]>([]);

  const data = ref<ItemType[] | null>(null);
  const categories = ref<string[]>([]);
  const selectedCategories = ref<string[]>([]);

  // Computed property to filter items based on selected categories
  const filteredItems = computed(() => {
    if (!data.value) return [];
    
    // If no categories are selected or all categories are selected, show all items
    if (selectedCategories.value.length === 0 || 
        selectedCategories.value.length === categories.value.length) {
      return data.value;
    }
    
    // Filter items based on selected categories
    return data.value.filter(item => 
      selectedCategories.value.includes(item.category)
    );
  });

  onMounted(async () => {
    try {
      const res = await fetch('https://fakestoreapi.com/products');
      data.value = await res.json();
      if (data.value) {
        categories.value = [...new Set(data.value.map((item: ItemType) => item.category))];
        // Initialize selectedCategories with all categories
        selectedCategories.value = [...categories.value];
      }
    } catch (error) {
      console.error('Failed to fetch products:', error);
    }
  });
  
  // Function to handle category selection
  const handleCategorySelected = (selected: string[]) => {
    selectedCategories.value = selected;
  };
  
  // Function to handle add to cart from Item component
  const openAddToCartModal = (item: ItemType) => {
    selectedItem.value = item;
    showModal.value = true;
  };
  
  // Function to handle add to cart from modal
  const handleAddToCart = (itemWithQuantity: CartItem) => {
    // Check if item is already in cart
    const existingItemIndex = cartItems.value.findIndex(
      item => item.id === itemWithQuantity.id
    );
    
    if (existingItemIndex >= 0) {
      // Update quantity if item is already in cart
      cartItems.value[existingItemIndex].quantity += itemWithQuantity.quantity;
      handleShowNotification(`Updated ${itemWithQuantity.title} quantity in cart`);
    } else {
      // Add new item to cart
      cartItems.value.push(itemWithQuantity);
      handleShowNotification(`Added ${itemWithQuantity.title} to cart`);
    }
    
    // Increment the cart counter
    cart.value += itemWithQuantity.quantity;
  };

  // Function to handle editing cart items
  const handleEditCartItem = (itemId: number, newQuantity: number) => {
    const itemIndex = cartItems.value.findIndex(item => item.id === itemId);
    
    if (itemIndex >= 0) {
      // Calculate the difference in quantity
      const quantityDifference = newQuantity - cartItems.value[itemIndex].quantity;
      
      // Update the item quantity
      cartItems.value[itemIndex].quantity = newQuantity;
      
      // Update the total cart count
      cart.value += quantityDifference;
    }
  };
  
  // Function to handle removing cart items
  const handleRemoveCartItem = (itemId: number) => {
    const itemIndex = cartItems.value.findIndex(item => item.id === itemId);
    
    if (itemIndex >= 0) {
      // Subtract the item's quantity from the cart total
      cart.value -= cartItems.value[itemIndex].quantity;
      
      // Remove the item from cartItems
      cartItems.value.splice(itemIndex, 1);
    }
  };

  // Function to handle updating cart item quantities
  const handleUpdateCartQuantity = (itemId: number, newQuantity: number) => {
    // Don't allow quantities less than 1
    if (newQuantity < 1) return;
    
    const itemIndex = cartItems.value.findIndex(item => item.id === itemId);
    
    if (itemIndex >= 0) {
      // Calculate the difference in quantity
      const quantityDifference = newQuantity - cartItems.value[itemIndex].quantity;
      
      // Update the item quantity
      cartItems.value[itemIndex].quantity = newQuantity;
      
      // Update the total cart count
      cart.value += quantityDifference;
    }
  };

  // Fix the cart clearing function to use the correct variable name
  const handleClearCart = () => {
    cartItems.value = [];
    cart.value = 0; // Changed from cartNumber.value to cart.value
  };
  provide('handleClearCart', handleClearCart);

  // Notification functionality
  const notification = reactive({
    message: '',
    visible: false,
    timeout: null as number | null
  });

  const handleShowNotification = (message: string) => {
    // Clear any existing timeout
    if (notification.timeout) {
      clearTimeout(notification.timeout);
    }
    
    // Set notification message and make it visible
    notification.message = message;
    notification.visible = true;
    
    // Hide after 3 seconds
    notification.timeout = setTimeout(() => {
      notification.visible = false;
    }, 2000);
  };
  provide('handleShowNotification', handleShowNotification);
  
  // Provide shared state and functions to child components
  provide('filteredItems', filteredItems);
  provide('categories', categories);
  provide('handleCategorySelected', handleCategorySelected);
  provide('openAddToCartModal', openAddToCartModal);
  provide('cartItems', cartItems);
  provide('handleRemoveCartItem', handleRemoveCartItem);
  provide('handleUpdateCartQuantity', handleUpdateCartQuantity);
</script>

<template>
  <NavBar 
    :cartNumber="cart" 
    :cartItems="cartItems" 
    @edit-cart-item="handleEditCartItem" 
    @remove-cart-item="handleRemoveCartItem" 
  />

  <div class="bg-gray-100 pb-8">
    <div class="container w-[90%] mx-auto">
      <router-view></router-view>
    </div>    
  </div>
  
  <AddToModal 
    :showModal="showModal" 
    :item="selectedItem" 
    @close="showModal = false" 
    @add-to-cart="handleAddToCart" 
  />

  <!-- Notification component -->
  <div 
    v-if="notification.visible"
    class="fixed top-20 right-4 bg-green-500 text-white p-4 rounded shadow-lg z-50 transition-opacity"
  >
    {{ notification.message }}
  </div>
</template>