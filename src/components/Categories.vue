<script setup lang="ts">
    defineProps<{
        categories: string[];
    }>();
</script>
<template>
    <div class="flex gap-4 my-4 justify-end" >
        <div v-for="category in categories" :key="category">
            <button class="btn bg-gray-300">{{ category }}</button>
        </div>        
    </div>
</template>