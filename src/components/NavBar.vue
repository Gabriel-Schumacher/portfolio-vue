<script setup lang="ts">
defineProps({
    cartNumber: {
        type: Number,
        default: 0
    }
});
</script>

<template>
    <div class="p-4 bg-primary">
        <ul class="container w-[90%] mx-auto flex gap-4 justify-end">
            <li><a href="#" class="hover:text-gray-300">Items</a></li>
            <li><a href="#" class="hover:text-gray-300">Cart ({{ cartNumber }})</a></li>
        </ul>        
    </div>
</template>