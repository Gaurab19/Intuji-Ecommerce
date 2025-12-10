<template>
  <PageLoader
    v-if="isLoading"
    :loading="isLoading"
  />
  <div
    v-else
    class="bg-gray-100 dark:bg-gray-800 py-8"
  >
    <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex flex-col md:flex-row -mx-4">
        <div class="md:flex-1 px-4">
          <div class="h-[460px] rounded-lg bg-gray-300 dark:bg-gray-700 mb-4">
            <img
              class="w-full h-full object-cover"
              :src="productInformation.images[0]"
              alt="Product Image"
            >
          </div>
          <div class="flex -mx-2 mb-4">
            <div class="w-1/2 px-2">
              <button class="w-full bg-gray-900 dark:bg-gray-600 text-white py-2 px-4 rounded-full font-bold hover:bg-gray-800 dark:hover:bg-gray-700">
                Add to Cart
              </button>
            </div>
            <div class="w-1/2 px-2">
              <button class="w-full bg-gray-200 dark:bg-gray-700 text-gray-800 dark:text-white py-2 px-4 rounded-full font-bold hover:bg-gray-300 dark:hover:bg-gray-600">
                Add to Wishlist
              </button>
            </div>
          </div>
        </div>
        <div class="md:flex-1 px-4">
          <h2 class="text-2xl font-bold text-gray-800 dark:text-white mb-2">
            {{ productInformation.title }}
          </h2>
          <p class="text-gray-600 dark:text-gray-300 text-sm mb-4">
            {{ productInformation.description }}
          </p>
          <div class="flex mb-5">
            <div class="mr-5">
              <span class="font-bold text-gray-700 dark:text-gray-300">Price:</span>
              <span class="text-gray-600 dark:text-gray-300">{{ productInformation.price }}</span>
            </div>
            <div class="mr-5">
              <span class="font-bold text-gray-700 dark:text-gray-300">Available Stocks:</span>
              <span class="text-gray-600 dark:text-gray-300">{{ productInformation.stock }}</span>
            </div>
            <div class="mr-5">
              <span class="font-bold text-gray-700 dark:text-gray-300">Rating:</span>
              <span class="text-gray-600 dark:text-gray-300">{{ productInformation.rating }}</span>
            </div>
          </div>
          <div>
            <span class="font-bold text-gray-700 dark:text-gray-300">Customer Reviews:</span>
            <p
              v-for="review in productInformation.reviews"
              :key="item"
              class="text-gray-600 dark:text-gray-300 text-sm mt-2"
            >
              =>{{ review.comment }}
            </p>
          </div>
          <div class="flex-mx-2 mb-4 mt-4">
            <div class="px-2">
              <NuxtLink
                to="/"
                class="w-full bg-red-200 dark:bg-gray-700 text-gray-800 dark:text-white py-2 px-4 rounded-full font-bold hover:bg-gray-300 dark:hover:bg-gray-600"
              >
                Go to Products Page
              </NuxtLink>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const { id } = useRoute().params
const isLoading = ref(false)
const productInformation = ref({})
async function fetchInitialData() {
  isLoading.value = true
  const res = await $fetch(`https://dummyjson.com/products/${id}`, {
    method: 'GET'
  })
  productInformation.value = res
  isLoading.value = false
}
fetchInitialData()
</script>
