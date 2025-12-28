<template>
  <!-- Loading State -->
  <div v-if="isLoading" class="min-h-screen bg-gray-50 flex items-center justify-center">
    <div class="text-center">
      <div class="inline-block animate-spin rounded-full h-16 w-16 border-b-2 border-indigo-600"></div>
      <p class="mt-4 text-gray-600">Loading product details...</p>
    </div>
  </div>

  <!-- Product Detail -->
  <div v-else-if="productInformation.id" class="min-h-screen bg-gradient-to-br from-gray-50 to-gray-100">
    <!-- Breadcrumb -->
    <div class="bg-white border-b">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4">
        <nav class="flex items-center space-x-2 text-sm">
          <NuxtLink to="/" class="text-indigo-600 hover:text-indigo-800 font-medium transition-colors">
            Home
          </NuxtLink>
          <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
          </svg>
          <NuxtLink to="/" class="text-indigo-600 hover:text-indigo-800 font-medium transition-colors">
            Products
          </NuxtLink>
          <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
          </svg>
          <span class="text-gray-500">{{ productInformation.category }}</span>
          <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
          </svg>
          <span class="text-gray-900 font-medium truncate max-w-xs">{{ productInformation.title }}</span>
        </nav>
      </div>
    </div>

    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 lg:gap-12">
        <!-- Left Column - Images -->
        <div class="space-y-4">
          <!-- Main Image -->
          <div class="relative bg-white rounded-2xl shadow-lg overflow-hidden group">
            <div class="aspect-square">
              <img
                :src="selectedImage"
                :alt="productInformation.title"
                class="w-full h-full object-cover transition-transform duration-500 group-hover:scale-110"
                @error="handleImageError"
              />
            </div>
            
            <!-- Discount Badge -->
            <div
              v-if="productInformation.discountPercentage > 0"
              class="absolute top-4 right-4 bg-gradient-to-r from-red-500 to-pink-500 text-white px-4 py-2 rounded-full text-sm font-bold shadow-lg animate-pulse"
            >
              -{{ Math.round(productInformation.discountPercentage) }}% OFF
            </div>

            <!-- Stock Badge -->
            <div
              :class="[
                'absolute top-4 left-4 px-4 py-2 rounded-full text-sm font-bold shadow-lg',
                productInformation.availabilityStatus === 'In Stock'
                  ? 'bg-green-500 text-white'
                  : 'bg-red-500 text-white'
              ]"
            >
              {{ productInformation.availabilityStatus }}
            </div>
          </div>

          <!-- Thumbnail Gallery -->
          <div v-if="productInformation.images && productInformation.images.length > 1" class="grid grid-cols-4 gap-3">
            <button
              v-for="(image, index) in productInformation.images"
              :key="index"
              @click="selectedImage = image"
              :class="[
                'relative aspect-square rounded-lg overflow-hidden border-2 transition-all duration-300',
                selectedImage === image
                  ? 'border-indigo-600 shadow-lg scale-105'
                  : 'border-gray-200 hover:border-indigo-300'
              ]"
            >
              <img
                :src="image"
                :alt="`${productInformation.title} - Image ${index + 1}`"
                class="w-full h-full object-cover"
              />
            </button>
          </div>

          <!-- Product Meta Info Cards -->
          <div class="grid grid-cols-2 gap-4">
            <div class="bg-white rounded-xl p-4 shadow-md">
              <div class="flex items-center space-x-3">
                <div class="p-3 bg-indigo-100 rounded-lg">
                  <svg class="w-6 h-6 text-indigo-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
                  </svg>
                </div>
                <div>
                  <p class="text-xs text-gray-500 font-medium">Weight</p>
                  <p class="text-sm font-bold text-gray-900">{{ productInformation.weight }}g</p>
                </div>
              </div>
            </div>

            <div class="bg-white rounded-xl p-4 shadow-md">
              <div class="flex items-center space-x-3">
                <div class="p-3 bg-green-100 rounded-lg">
                  <svg class="w-6 h-6 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                  </svg>
                </div>
                <div>
                  <p class="text-xs text-gray-500 font-medium">Warranty</p>
                  <p class="text-sm font-bold text-gray-900">{{ productInformation.warrantyInformation }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Right Column - Product Info -->
        <div class="space-y-6">
          <!-- Title and Brand -->
          <div>
            <div class="flex items-center space-x-2 mb-2">
              <span class="px-3 py-1 bg-indigo-100 text-indigo-700 rounded-full text-xs font-semibold uppercase">
                {{ productInformation.brand }}
              </span>
              <span class="px-3 py-1 bg-gray-100 text-gray-700 rounded-full text-xs font-semibold uppercase">
                {{ productInformation.category }}
              </span>
            </div>
            <h1 class="text-3xl lg:text-4xl font-bold text-gray-900 mb-2">
              {{ productInformation.title }}
            </h1>
            <p class="text-gray-600 text-base leading-relaxed">
              {{ productInformation.description }}
            </p>
          </div>

          <!-- Rating -->
          <div class="flex items-center space-x-4 pb-6 border-b border-gray-200">
            <div class="flex items-center space-x-1">
              <svg
                v-for="n in 5"
                :key="n"
                :class="[
                  'w-5 h-5',
                  n <= Math.floor(productInformation.rating) ? 'fill-yellow-400' : 'fill-gray-300'
                ]"
                viewBox="0 0 24 24"
              >
                <path d="M12 17.27L18.18 21l-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.73L5.82 21z"/>
              </svg>
            </div>
            <span class="text-gray-700 font-semibold">
              {{ productInformation.rating.toFixed(1) }}
            </span>
            <span class="text-gray-500">
              ({{ productInformation.reviews?.length || 0 }} reviews)
            </span>
          </div>

          <!-- Price Section -->
          <div class="bg-gradient-to-r from-indigo-50 to-purple-50 rounded-2xl p-6 border border-indigo-100">
            <div class="flex items-baseline space-x-3 mb-2">
              <span class="text-4xl font-bold text-indigo-600">
                ${{ productInformation.price.toFixed(2) }}
              </span>
              <span class="text-2xl text-gray-400 line-through">
                ${{ originalPrice.toFixed(2) }}
              </span>
            </div>
            <p class="text-sm text-gray-600">
              You save: <span class="font-bold text-green-600">${{ savings.toFixed(2) }}</span>
            </p>
          </div>

          <!-- Product Details Grid -->
          <div class="grid grid-cols-2 gap-4">
            <div class="bg-white rounded-xl p-4 border border-gray-200">
              <p class="text-xs text-gray-500 font-medium mb-1">Stock Available</p>
              <p class="text-2xl font-bold text-gray-900">{{ productInformation.stock }}</p>
            </div>
            <div class="bg-white rounded-xl p-4 border border-gray-200">
              <p class="text-xs text-gray-500 font-medium mb-1">Min. Order</p>
              <p class="text-2xl font-bold text-gray-900">{{ productInformation.minimumOrderQuantity }}</p>
            </div>
            <div class="bg-white rounded-xl p-4 border border-gray-200">
              <p class="text-xs text-gray-500 font-medium mb-1">SKU</p>
              <p class="text-sm font-bold text-gray-900">{{ productInformation.sku }}</p>
            </div>
            <div class="bg-white rounded-xl p-4 border border-gray-200">
              <p class="text-xs text-gray-500 font-medium mb-1">Shipping</p>
              <p class="text-sm font-bold text-gray-900">{{ productInformation.shippingInformation }}</p>
            </div>
          </div>

          <!-- Quantity Selector -->
          <div class="bg-white rounded-xl p-6 border border-gray-200">
            <label class="text-sm font-semibold text-gray-900 block mb-3">Quantity</label>
            <div class="flex items-center space-x-4">
              <button
                @click="decrementQuantity"
                class="w-12 h-12 rounded-lg border-2 border-gray-300 hover:border-indigo-500 hover:bg-indigo-50 transition-all font-bold text-lg"
              >
                -
              </button>
              <input
                v-model.number="quantity"
                type="number"
                min="1"
                :max="productInformation.stock"
                class="w-20 h-12 text-center text-xl font-bold border-2 border-gray-300 rounded-lg focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 outline-none"
              />
              <button
                @click="incrementQuantity"
                class="w-12 h-12 rounded-lg border-2 border-gray-300 hover:border-indigo-500 hover:bg-indigo-50 transition-all font-bold text-lg"
              >
                +
              </button>
            </div>
          </div>

          <!-- Action Buttons -->
          <div class="grid grid-cols-2 gap-4">
            <button
              @click="addToCart"
              class="flex items-center justify-center space-x-2 bg-gradient-to-r from-indigo-600 to-purple-600 text-white py-4 px-6 rounded-xl font-bold hover:from-indigo-700 hover:to-purple-700 transition-all shadow-lg hover:shadow-xl transform hover:-translate-y-0.5"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />
              </svg>
              <span>Add to Cart</span>
            </button>
            <button
              @click="toggleWishlist"
              :class="[
                'flex items-center justify-center space-x-2 py-4 px-6 rounded-xl font-bold transition-all shadow-md hover:shadow-lg transform hover:-translate-y-0.5',
                isInWishlist
                  ? 'bg-red-500 text-white hover:bg-red-600'
                  : 'bg-white text-gray-800 border-2 border-gray-300 hover:border-red-500 hover:text-red-500'
              ]"
            >
              <svg class="w-5 h-5" :fill="isInWishlist ? 'currentColor' : 'none'" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
              </svg>
              <span>{{ isInWishlist ? 'In Wishlist' : 'Add to Wishlist' }}</span>
            </button>
          </div>

          <!-- Tags -->
          <div v-if="productInformation.tags && productInformation.tags.length > 0" class="flex flex-wrap gap-2">
            <span
              v-for="tag in productInformation.tags"
              :key="tag"
              class="px-3 py-1 bg-gray-100 text-gray-700 rounded-full text-xs font-medium"
            >
              #{{ tag }}
            </span>
          </div>
        </div>
      </div>

      <!-- Customer Reviews Section -->
      <div v-if="productInformation.reviews && productInformation.reviews.length > 0" class="mt-16">
        <div class="bg-white rounded-2xl shadow-lg p-8">
          <div class="flex items-center justify-between mb-8">
            <h2 class="text-2xl font-bold text-gray-900">Customer Reviews</h2>
            <div class="flex items-center space-x-2">
              <span class="text-3xl font-bold text-gray-900">{{ productInformation.rating.toFixed(1) }}</span>
              <div>
                <div class="flex">
                  <svg
                    v-for="n in 5"
                    :key="n"
                    :class="[
                      'w-4 h-4',
                      n <= Math.floor(productInformation.rating) ? 'fill-yellow-400' : 'fill-gray-300'
                    ]"
                    viewBox="0 0 24 24"
                  >
                    <path d="M12 17.27L18.18 21l-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.73L5.82 21z"/>
                  </svg>
                </div>
                <p class="text-xs text-gray-500">{{ productInformation.reviews.length }} reviews</p>
              </div>
            </div>
          </div>

          <div class="space-y-6">
            <div
              v-for="(review, index) in productInformation.reviews"
              :key="index"
              class="border-b border-gray-200 pb-6 last:border-b-0 last:pb-0"
            >
              <div class="flex items-start space-x-4">
                <div class="flex-shrink-0">
                  <div class="w-12 h-12 bg-gradient-to-br from-indigo-400 to-purple-500 rounded-full flex items-center justify-center text-white font-bold text-lg">
                    {{ review.reviewerName.charAt(0) }}
                  </div>
                </div>
                <div class="flex-1">
                  <div class="flex items-center justify-between mb-2">
                    <div>
                      <h4 class="font-semibold text-gray-900">{{ review.reviewerName }}</h4>
                      <p class="text-sm text-gray-500">{{ formatDate(review.date) }}</p>
                    </div>
                    <div class="flex">
                      <svg
                        v-for="n in 5"
                        :key="n"
                        :class="[
                          'w-4 h-4',
                          n <= review.rating ? 'fill-yellow-400' : 'fill-gray-300'
                        ]"
                        viewBox="0 0 24 24"
                      >
                        <path d="M12 17.27L18.18 21l-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.73L5.82 21z"/>
                      </svg>
                    </div>
                  </div>
                  <p class="text-gray-700 leading-relaxed">{{ review.comment }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Additional Info -->
      <div class="mt-8 grid grid-cols-1 md:grid-cols-3 gap-6">
        <div class="bg-white rounded-xl p-6 shadow-md text-center">
          <div class="w-16 h-16 bg-blue-100 rounded-full flex items-center justify-center mx-auto mb-4">
            <svg class="w-8 h-8 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 8h14M5 8a2 2 0 110-4h14a2 2 0 110 4M5 8v10a2 2 0 002 2h10a2 2 0 002-2V8m-9 4h4" />
            </svg>
          </div>
          <h3 class="font-bold text-gray-900 mb-2">Return Policy</h3>
          <p class="text-sm text-gray-600">{{ productInformation.returnPolicy }}</p>
        </div>

        <div class="bg-white rounded-xl p-6 shadow-md text-center">
          <div class="w-16 h-16 bg-green-100 rounded-full flex items-center justify-center mx-auto mb-4">
            <svg class="w-8 h-8 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
            </svg>
          </div>
          <h3 class="font-bold text-gray-900 mb-2">Warranty</h3>
          <p class="text-sm text-gray-600">{{ productInformation.warrantyInformation }}</p>
        </div>

        <div class="bg-white rounded-xl p-6 shadow-md text-center">
          <div class="w-16 h-16 bg-purple-100 rounded-full flex items-center justify-center mx-auto mb-4">
            <svg class="w-8 h-8 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z" />
            </svg>
          </div>
          <h3 class="font-bold text-gray-900 mb-2">Fast Shipping</h3>
          <p class="text-sm text-gray-600">{{ productInformation.shippingInformation }}</p>
        </div>
      </div>

      <!-- Back Button -->
      <div class="mt-12 text-center">
        <NuxtLink
          to="/"
          class="inline-flex items-center space-x-2 text-indigo-600 hover:text-indigo-800 font-semibold transition-colors group"
        >
          <svg class="w-5 h-5 transform group-hover:-translate-x-1 transition-transform" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
          </svg>
          <span>Back to Products</span>
        </NuxtLink>
      </div>
    </div>
  </div>

  <!-- Error State -->
  <div v-else class="min-h-screen bg-gray-50 flex items-center justify-center">
    <div class="text-center">
      <svg class="w-24 h-24 text-red-300 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
      </svg>
      <h3 class="text-2xl font-bold text-gray-900 mb-2">Product Not Found</h3>
      <p class="text-gray-600 mb-6">The product you're looking for doesn't exist.</p>
      <NuxtLink
        to="/"
        class="inline-block px-6 py-3 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700 transition-colors font-medium"
      >
        Browse Products
      </NuxtLink>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

const { id } = useRoute().params
const isLoading = ref(true)
const productInformation = ref<any>({})
const selectedImage = ref('')
const quantity = ref(1)
const isInWishlist = ref(false)

const originalPrice = computed(() => {
  if (!productInformation.value.price || !productInformation.value.discountPercentage) return 0
  return productInformation.value.price / (1 - productInformation.value.discountPercentage / 100)
})

const savings = computed(() => {
  return originalPrice.value - productInformation.value.price
})

async function fetchInitialData() {
  isLoading.value = true
  try {
    const res = await fetch(`https://dummyjson.com/products/${id}`)
    if (!res.ok) throw new Error('Product not found')
    
    const data = await res.json()
    productInformation.value = data
    selectedImage.value = data.images?.[0] || data.thumbnail || ''
  } catch (error) {
    console.error('Error fetching product:', error)
  } finally {
    isLoading.value = false
  }
}

function handleImageError(event: Event) {
  const target = event.target as HTMLImageElement
  target.src = 'https://via.placeholder.com/600x600?text=No+Image'
}

function incrementQuantity() {
  if (quantity.value < productInformation.value.stock) {
    quantity.value++
  }
}

function decrementQuantity() {
  if (quantity.value > 1) {
    quantity.value--
  }
}

function addToCart() {
  alert(`Added ${quantity.value} item(s) to cart!`)
}

function toggleWishlist() {
  isInWishlist.value = !isInWishlist.value
}

function formatDate(dateString: string) {
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', { 
    year: 'numeric', 
    month: 'long', 
    day: 'numeric' 
  })
}

onMounted(() => {
  fetchInitialData()
})
</script>

<style scoped>
/* Smooth animations */
* {
  transition-property: background-color, border-color, color, fill, stroke, opacity, transform;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 150ms;
}

/* Prevent number input arrows */
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type="number"] {
  -moz-appearance: textfield;
}
</style>