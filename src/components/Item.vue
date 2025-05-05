<script setup lang="ts">
   import { computed } from 'vue';
    const props = defineProps<{
        item: {
            id: number;
            title: string;
            description: string;
            price: number;
            image: string;
        };
    }>();
 
    // Define emits for the component
    const emit = defineEmits(['add-to-cart']);

    const maxDescriptionLength = 100;
    const truncatedDescription = computed(() => {
        return props.item.description.length > maxDescriptionLength
            ? props.item.description.slice(0, maxDescriptionLength) + '...'
            : props.item.description;
    });

    const maxTitleLength = 50;
    const truncatedTitle = computed(() => {
        return props.item.title.length > maxTitleLength
            ? props.item.title.slice(0, maxTitleLength) + '...'
            : props.item.title;
    });

    // Function to handle add to cart
    const handleAddToCart = () => {
        emit('add-to-cart', props.item);
    };
</script>

<template>
    <div class="card bg-white shadow-md rounded-lg p-4 h-[30rem] flex flex-col">
        <h2 @click="handleAddToCart" class="font-bold cursor-pointer">{{ truncatedTitle }}</h2>
        <div class="flex justify-center items-center my-4 h-[14rem]">
            <img :src="item.image" alt="Product Image" class="h-full w-auto"/>            
        </div>
        <div>
            <p>{{ truncatedDescription }}</p>            
        </div>
        <div class="mt-auto">
            <p>Price: ${{ item.price }}</p>
            <button @click="handleAddToCart" type="button" class="btn bg-primary">Add to Cart</button>    
        </div>
    </div>
</template>