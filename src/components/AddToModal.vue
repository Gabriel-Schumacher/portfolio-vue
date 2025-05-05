<template>
  <div class="modal-overlay" v-if="showModal" @click.self="closeModal">
    <div class="bg-white rounded-lg shadow-lg overflow-y-auto w-[90%]">
      <div class="modal-header">
        <h3>Add to Cart</h3>
        <button class="close-btn" @click="closeModal">&times;</button>
      </div>
      
      <div class="modal-body" v-if="item">
        <div class="flex">
          <div class="product-image-container">
            <div class="main-image-container" 
                 @mousemove="handleMouseMove" 
                 @mouseenter="handleMouseEnter" 
                 @mouseleave="handleMouseLeave"
                 ref="imageContainer">
              <img v-if="item.image" :src="item.image" :alt="item.title" class="main-image">
              <div class="zoom-lens" v-show="isImageMagnified" :style="lensStyle"></div>
              <div class="zoom-hint" v-if="!isImageMagnified">
                <span>Hover to zoom</span>
              </div>
            </div>
            <div class="magnified-view" v-if="isImageMagnified" :style="magnifiedViewStyle">
            </div>
          </div>
          <div class="item-info">
            <h4>{{ item.title }}</h4>
            <p class="price">${{ item.price.toFixed(2) }}</p>
            <p class="description">{{ item.description }}</p>
          </div>
        </div>
        
        <div class="quantity-selector">
          <span>Quantity:</span>
          <div class="quantity-controls">
            <button @click="decreaseQuantity" :disabled="quantity <= 1">-</button>
            <input type="number" v-model.number="quantity" min="1" @change="validateQuantity">
            <button @click="increaseQuantity">+</button>
          </div>
        </div>
        
        <p class="subtotal">Subtotal: ${{ (item.price * quantity).toFixed(2) }}</p>
      </div>
      
      <div class="flex justify-end p-4 gap-4">
        <button class="btn bg-gray-200" @click="closeModal">Cancel</button>
        <button class="btn bg-green-400" @click="addToCart">Add to Cart</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AddToModal',
  props: {
    showModal: {
      type: Boolean,
      default: false
    },
    item: {
      type: Object,
      required: true,
      default: () => ({
        id: 0,
        title: '',
        price: 0,
        description: '',
        image: ''
      })
    }
  },
  data() {
    return {
      quantity: 1,
      isImageMagnified: false,
      mousePosition: { x: 0, y: 0 },
      zoomLevel: 2.5, // Magnification factor
      lensSize: { width: 60, height: 60 }
    }
  },
  computed: {
    lensStyle() {
      return {
        left: `${this.mousePosition.x - this.lensSize.width / 2}px`,
        top: `${this.mousePosition.y - this.lensSize.height / 2}px`,
        width: `${this.lensSize.width}px`,
        height: `${this.lensSize.height}px`
      }
    },
    magnifiedViewStyle() {
      // Calculate background position based on mouse position relative to image
      if (!this.$refs.imageContainer) return {};
      
      const rect = this.$refs.imageContainer.getBoundingClientRect();
      const xRatio = this.mousePosition.x / rect.width;
      const yRatio = this.mousePosition.y / rect.height;
      
      return {
        backgroundImage: this.item.image ? `url(${this.item.image})` : 'none',
        backgroundPosition: `${xRatio * 100}% ${yRatio * 100}%`,
        backgroundSize: `${this.zoomLevel * 100}%`
      }
    }
  },
  methods: {
    closeModal() {
      this.$emit('close');
    },
    increaseQuantity() {
      this.quantity += 1;
    },
    decreaseQuantity() {
      if (this.quantity > 1) {
        this.quantity -= 1;
      }
    },
    validateQuantity() {
      // Ensure quantity is at least 1
      if (this.quantity < 1 || isNaN(this.quantity)) {
        this.quantity = 1;
      } else {
        // Round to whole number
        this.quantity = Math.floor(this.quantity);
      }
    },
    addToCart() {
      this.$emit('add-to-cart', { 
        ...this.item, 
        quantity: this.quantity 
      });
      this.quantity = 1; // Reset quantity
      this.closeModal();
    },
    handleMouseMove(event) {
      const rect = event.currentTarget.getBoundingClientRect();
      this.mousePosition = {
        x: event.clientX - rect.left,
        y: event.clientY - rect.top
      };
    },
    handleMouseEnter() {
      this.isImageMagnified = true;
    },
    handleMouseLeave() {
      this.isImageMagnified = false;
    }
  }
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 100;
}

.modal-container {
  background-color: white;
  border-radius: 8px;
  width: 90%;
  max-width: 500px;
  max-height: 90vh;
  overflow-y: auto;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.modal-header {
  padding: 15px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eee;
}

.close-btn {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #666;
}

.modal-body {
  padding: 20px;
}

.product-image-container {
  display: flex;
  flex-direction: column;
  margin-right: 15px;
  position: relative;
  width: 200px;
}

.main-image-container {
  position: relative;
  width: 200px;
  height: 200px;
  cursor: crosshair;
  overflow: hidden;
  border: 1px solid #eee;
}

.main-image {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.zoom-lens {
  position: absolute;
  border: 1px solid #ccc;
  background-color: rgba(255, 255, 255, 0.3);
  pointer-events: none;
}

.zoom-hint {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: rgba(255, 255, 255, 0.8);
  padding: 3px 6px;
  border-radius: 3px;
  font-size: 0.7rem;
  color: #666;
  pointer-events: none;
}

.magnified-view {
  width: 375px;
  height: 375px;
  border: 1px solid #ddd;
  position: absolute;
  left: 200px;
  top: 0px;
  z-index: 10;
  background-repeat: no-repeat;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  background-color: #fff;
}

.item-info {
  flex: 1;
  min-width: 200px;
}

.price {
  font-weight: bold;
  color: #e64a19;
  margin-bottom: 10px;
}

.description {
  color: #666;
  font-size: 0.9rem;
}

.quantity-selector {
  display: flex;
  align-items: center;
  margin: 15px 0;
}

.quantity-controls {
  display: flex;
  margin-left: 10px;
}

.quantity-controls button {
  width: 30px;
  height: 30px;
  border: 1px solid #ddd;
  background-color: #f8f8f8;
  cursor: pointer;
}

.quantity-controls input {
  width: 40px;
  height: 30px;
  text-align: center;
  border: 1px solid #ddd;
  border-left: none;
  border-right: none;
}

.subtotal {
  font-weight: bold;
  text-align: right;
  margin-top: 15px;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type=number] {
  appearance: textfield;
  -moz-appearance: textfield;
}

/* Adjust other styles for better mobile responsiveness */
@media (max-width: 640px) {
  .product-image-container {
    width: 100%;
    margin-right: 0;
    margin-bottom: 15px;
  }
  
  .main-image-container {
    width: 100%;
    height: auto;
    aspect-ratio: 1/1;
  }
  
  .magnified-view {
    display: none; /* Hide zoom on small screens */
  }
  
  .item-info {
    width: 100%;
  }
}
</style>
