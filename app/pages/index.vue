<template>
  <div class="p-4 mx-auto lg:max-w-screen-xl">
    <!-- Header -->
    <div class="border-b border-gray-300 pb-4 mb-6">
      <h2 class="text-slate-900 text-2xl font-bold">
        Hot list
      </h2>
      <p class="text-slate-600 mt-2">
        Check out the most popular and trending products.
      </p>
    </div>

    <div class="flex flex-col lg:flex-row gap-6">
      <!-- Sidebar Filter -->
      <aside class="w-full lg:max-w-[280px] shrink-0">
        <div class="bg-white shadow-md rounded-lg px-6 py-6 sticky top-4">
          <div class="flex items-center justify-between border-b border-gray-300 pb-3 mb-6">
            <h3 class="text-slate-900 text-lg font-semibold">
              Filters
            </h3>
            <button
              v-if="hasActiveFilters"
              @click="clearAllFilters"
              class="text-sm text-indigo-600 hover:text-indigo-700 font-medium transition-colors"
            >
              Clear All
            </button>
          </div>

          <!-- Search -->
          <div class="mb-6">
            <label class="text-slate-900 text-sm font-semibold block mb-2">
              Search Products
            </label>
            <div class="relative">
              <input
                v-model="searchProductKey"
                @input="handleSearchInput"
                type="text"
                placeholder="Search products..."
                class="w-full px-4 py-2.5 pr-10 rounded-lg border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 transition-all outline-none text-sm"
              />
              <svg 
                v-if="searchProductKey"
                @click="clearSearch"
                class="absolute right-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400 hover:text-gray-600 cursor-pointer"
                fill="none" 
                stroke="currentColor" 
                viewBox="0 0 24 24"
              >
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
              </svg>
              <svg 
                v-else
                class="absolute right-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400"
                fill="none" 
                stroke="currentColor" 
                viewBox="0 0 24 24"
              >
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
              </svg>
            </div>
          </div>

          <!-- Category Filter -->
          <div>
            <label class="text-slate-900 text-sm font-semibold block mb-3">
              Categories
            </label>
            <div class="max-h-96 overflow-y-auto space-y-2 pr-2 custom-scrollbar">
              <label
                v-for="category in productCategoryList"
                :key="category.value"
                class="flex items-center p-2.5 rounded-lg hover:bg-indigo-50 cursor-pointer transition-colors group"
              >
                <input
                  type="radio"
                  :value="category.value"
                  v-model="selectedProductCategory"
                  class="w-4 h-4 text-indigo-600 border-gray-300 focus:ring-indigo-500"
                />
                <span class="ml-3 text-sm text-gray-700 group-hover:text-indigo-700 flex-1">
                  {{ category.label }}
                </span>
              </label>
            </div>
          </div>

          <!-- Sort Options -->
          <div class="mt-6 pt-6 border-t border-gray-300">
            <label class="text-slate-900 text-sm font-semibold block mb-3">
              Sort By
            </label>
            <select
              v-model="sortOption"
              class="w-full px-4 py-2.5 rounded-lg border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 transition-all outline-none text-sm"
            >
              <option value="">Default</option>
              <option value="price-asc">Price: Low to High</option>
              <option value="price-desc">Price: High to Low</option>
              <option value="rating">Rating: High to Low</option>
            </select>
          </div>
        </div>
      </aside>

      <!-- Main Content -->
      <main class="flex-1">
        <!-- Active Filters -->
        <div v-if="hasActiveFilters" class="flex flex-wrap gap-2 mb-4">
          <span
            v-if="searchProductKey"
            class="inline-flex items-center gap-2 px-3 py-1.5 bg-indigo-100 text-indigo-700 rounded-full text-sm font-medium"
          >
            Search: "{{ searchProductKey }}"
            <button @click="clearSearch" class="hover:text-indigo-900">
              <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
                <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd" />
              </svg>
            </button>
          </span>
          <span
            v-if="selectedProductCategory"
            class="inline-flex items-center gap-2 px-3 py-1.5 bg-indigo-100 text-indigo-700 rounded-full text-sm font-medium"
          >
            {{ getCategoryLabel(selectedProductCategory) }}
            <button @click="clearCategory" class="hover:text-indigo-900">
              <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
                <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd" />
              </svg>
            </button>
          </span>
        </div>

        <!-- Loading State -->
        <div v-if="isLoading" class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 sm:gap-6">
          <div
            v-for="n in 8"
            :key="n"
            class="border border-gray-200 rounded-lg p-2 animate-pulse"
          >
            <div class="w-full aspect-square bg-gray-200 rounded-md mb-3"></div>
            <div class="h-4 bg-gray-200 rounded mb-2"></div>
            <div class="h-3 bg-gray-200 rounded w-3/4 mb-2"></div>
            <div class="h-3 bg-gray-200 rounded w-1/2"></div>
          </div>
        </div>

        <!-- Empty State -->
        <div
          v-else-if="products.length === 0"
          class="flex flex-col items-center justify-center py-16 px-4"
        >
          <svg class="w-24 h-24 text-gray-300 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9.172 16.172a4 4 0 015.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
          <h3 class="text-xl font-semibold text-gray-700 mb-2">No products found</h3>
          <p class="text-gray-500 text-center mb-4">
            Try adjusting your filters or search terms
          </p>
          <button
            @click="clearAllFilters"
            class="px-6 py-2.5 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700 transition-colors font-medium"
          >
            Clear Filters
          </button>
        </div>

        <!-- Error State -->
        <div
          v-else-if="error"
          class="flex flex-col items-center justify-center py-16 px-4"
        >
          <svg class="w-24 h-24 text-red-300 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
          </svg>
          <h3 class="text-xl font-semibold text-gray-700 mb-2">Something went wrong</h3>
          <p class="text-gray-500 text-center mb-4">{{ error }}</p>
          <button
            @click="retryFetch"
            class="px-6 py-2.5 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700 transition-colors font-medium"
          >
            Try Again
          </button>
        </div>

        <!-- Products Grid -->
        <div v-else class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 sm:gap-6">
          <article
            v-for="product in sortedProducts"
            :key="product.id"
            class="flex flex-col border border-gray-200 shadow-sm rounded-lg overflow-hidden transition-all hover:shadow-xl hover:border-indigo-300 group bg-white"
            @click="navigateTo(`/${product.id}`)"
          >
            <div class="relative w-full bg-slate-50 overflow-hidden">
              <img
                :src="product.images[0]"
                :alt="product.title"
                @error="handleImageError"
                class="w-full aspect-square object-cover object-top group-hover:scale-105 transition-transform duration-300"
                loading="lazy"
              />
              <div
                v-if="product.discountPercentage > 0"
                class="absolute top-2 right-2 bg-red-500 text-white px-2 py-1 rounded-md text-xs font-bold shadow-lg"
              >
                -{{ Math.round(product.discountPercentage) }}%
              </div>
            </div>
            
            <div class="p-3 flex-1 flex flex-col">
              <h3 class="text-sm sm:text-[15px] font-semibold text-slate-900 line-clamp-2 group-hover:text-indigo-600 transition-colors mb-2">
                {{ product.title }}
              </h3>
              <p class="text-xs text-slate-600 line-clamp-1 mb-2">{{ product.description }}</p>
              <p class="text-xs text-red-600 font-medium mb-2">{{ product.brand }}</p>
              
              <div class="flex items-center gap-1 mb-3">
                <div class="flex">
                  <svg
                    v-for="n in 5"
                    :key="n"
                    xmlns="http://www.w3.org/2000/svg"
                    :class="[
                      'w-3 h-3 sm:w-3.5 sm:h-3.5 shrink-0',
                      n <= Math.floor(product.rating) ? 'fill-yellow-400' : 'fill-gray-200'
                    ]"
                    viewBox="0 0 24 24"
                  >
                    <path d="M12 17.27L18.18 21l-1.64-7.03L22 9.24l-7.19-.61L12 2 9.19 8.63 2 9.24l5.46 4.73L5.82 21z"/>
                  </svg>
                </div>
                <span class="ml-1 text-xs font-medium text-slate-600">
                  {{ product.rating.toFixed(1) }} ({{ product?.reviews?.length || 0 }})
                </span>
              </div>
              
              <div class="mt-auto">
                <div class="flex items-center gap-2 mb-3">
                  <span class="text-slate-400 text-xs line-through">
                    ${{ (product.price / (1 - product.discountPercentage / 100)).toFixed(2) }}
                  </span>
                  <span class="text-indigo-600 font-bold text-base sm:text-lg">
                    ${{ product.price.toFixed(2) }}
                  </span>
                </div>
                
                <button
                  class="w-full px-3 py-2 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700 transition-colors text-sm font-medium shadow-sm hover:shadow-md"
                >
                  Add to Cart
                </button>
              </div>
            </div>
          </article>
        </div>

        <!-- Enhanced Pagination -->
        <div
          v-if="paginationData.total > 0 && !isLoading"
          class="flex flex-col sm:flex-row items-center justify-between bg-white border border-gray-200 px-6 py-4 rounded-lg shadow-sm mt-8"
        >
          <!-- Info -->
          <div class="mb-4 sm:mb-0">
            <p class="text-sm text-gray-700">
              Showing
              <span class="font-semibold text-indigo-600">{{ rangeStart }}</span>
              to
              <span class="font-semibold text-indigo-600">{{ rangeEnd }}</span>
              of
              <span class="font-semibold text-indigo-600">{{ paginationData.total }}</span>
              results
            </p>
          </div>

          <!-- Pagination Controls -->
          <nav aria-label="Pagination" class="flex items-center gap-2">
            <button
              @click="previousPage"
              :disabled="currentPage === 1"
              :class="[
                'px-3 py-2 rounded-lg border text-sm font-medium transition-all flex items-center gap-1',
                currentPage === 1
                  ? 'border-gray-200 bg-gray-50 text-gray-400 cursor-not-allowed'
                  : 'border-gray-300 bg-white text-gray-700 hover:bg-indigo-50 hover:border-indigo-400 hover:text-indigo-600'
              ]"
            >
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
              </svg>
              <span class="hidden sm:inline">Previous</span>
            </button>

            <div class="flex items-center gap-1">
              <button
                v-for="(page, index) in visiblePages"
                :key="index"
                @click="page !== '...' && goToPage(page)"
                :disabled="page === '...'"
                :class="[
                  'min-w-[40px] h-10 rounded-lg text-sm font-semibold transition-all',
                  page === currentPage
                    ? 'bg-indigo-600 text-white shadow-md'
                    : page === '...'
                    ? 'bg-transparent text-gray-400 cursor-default'
                    : 'bg-white text-gray-700 border border-gray-300 hover:bg-indigo-50 hover:border-indigo-400 hover:text-indigo-600'
                ]"
              >
                {{ page }}
              </button>
            </div>

            <button
              @click="nextPage"
              :disabled="currentPage === getTotalPages"
              :class="[
                'px-3 py-2 rounded-lg border text-sm font-medium transition-all flex items-center gap-1',
                currentPage === getTotalPages
                  ? 'border-gray-200 bg-gray-50 text-gray-400 cursor-not-allowed'
                  : 'border-gray-300 bg-white text-gray-700 hover:bg-indigo-50 hover:border-indigo-400 hover:text-indigo-600'
              ]"
            >
              <span class="hidden sm:inline">Next</span>
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
              </svg>
            </button>
          </nav>
        </div>

        <!-- Back to Top Button -->
        <transition name="fade">
          <button
            v-if="showBackToTop"
            @click="scrollToTop"
            class="fixed bottom-8 right-8 p-3 bg-indigo-600 text-white rounded-full shadow-lg hover:bg-indigo-700 transition-all hover:scale-110 z-50"
            aria-label="Back to top"
          >
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 10l7-7m0 0l7 7m-7-7v18" />
            </svg>
          </button>
        </transition>
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'

interface Product {
  id: number
  title: string
  description: string
  price: number
  discountPercentage: number
  rating: number
  brand: string
  category: string
  images: string[]
  reviews?: any[]
}

interface PaginationData {
  limit: number
  skip: number
  total: number
}

interface Category {
  label: string
  value: string
}

const ITEMS_PER_PAGE = 20
const DEBOUNCE_DELAY = 500

const paginationData = ref<PaginationData>({
  limit: ITEMS_PER_PAGE,
  skip: 0,
  total: 0
})

const productCategoryList = ref<Category[]>([])
const products = ref<Product[]>([])
const selectedProductCategory = ref<string>('')
const isLoading = ref(true)
const searchProductKey = ref('')
const sortOption = ref('')
const error = ref('')
const showBackToTop = ref(false)
let searchTimeout: NodeJS.Timeout | null = null
let abortController: AbortController | null = null

// Computed Properties
const currentPage = computed(() => {
  return Math.floor(paginationData.value.skip / paginationData.value.limit) + 1
})

const getTotalPages = computed(() => {
  return Math.ceil(paginationData.value.total / paginationData.value.limit)
})

const rangeStart = computed(() => {
  return Math.min(paginationData.value.skip + 1, paginationData.value.total)
})

const rangeEnd = computed(() => {
  return Math.min(paginationData.value.skip + paginationData.value.limit, paginationData.value.total)
})

const hasActiveFilters = computed(() => {
  return searchProductKey.value.trim() !== '' || selectedProductCategory.value !== ''
})

const visiblePages = computed(() => {
  const total = getTotalPages.value
  const current = currentPage.value
  const pages: (number | string)[] = []
  
  if (total <= 7) {
    for (let i = 1; i <= total; i++) {
      pages.push(i)
    }
  } else {
    if (current <= 4) {
      for (let i = 1; i <= 5; i++) pages.push(i)
      pages.push('...')
      pages.push(total)
    } else if (current >= total - 3) {
      pages.push(1)
      pages.push('...')
      for (let i = total - 4; i <= total; i++) pages.push(i)
    } else {
      pages.push(1)
      pages.push('...')
      for (let i = current - 1; i <= current + 1; i++) pages.push(i)
      pages.push('...')
      pages.push(total)
    }
  }
  
  return pages
})

const sortedProducts = computed(() => {
  if (!sortOption.value) return products.value
  
  const sorted = [...products.value]
  
  switch (sortOption.value) {
    case 'price-asc':
      return sorted.sort((a, b) => a.price - b.price)
    case 'price-desc':
      return sorted.sort((a, b) => b.price - a.price)
    case 'rating':
      return sorted.sort((a, b) => b.rating - a.rating)
    default:
      return sorted
  }
})

// API Functions
async function fetchProducts() {
  if (abortController) {
    abortController.abort()
  }
  
  abortController = new AbortController()
  isLoading.value = true
  error.value = ''
  
  try {
    let url = ''
    
    if (searchProductKey.value.trim()) {
      url = `https://dummyjson.com/products/search?q=${encodeURIComponent(searchProductKey.value)}&limit=${paginationData.value.limit}&skip=${paginationData.value.skip}`
    } else if (selectedProductCategory.value) {
      url = `https://dummyjson.com/products/category/${selectedProductCategory.value}?limit=${paginationData.value.limit}&skip=${paginationData.value.skip}`
    } else {
      url = `https://dummyjson.com/products?limit=${paginationData.value.limit}&skip=${paginationData.value.skip}`
    }
    
    const res = await fetch(url, { signal: abortController.signal })
    
    if (!res.ok) throw new Error('Failed to fetch products')
    
    const data = await res.json()
    
    paginationData.value = {
      limit: data.limit,
      skip: data.skip,
      total: data.total
    }
    products.value = data.products
  } catch (err: any) {
    if (err.name !== 'AbortError') {
      error.value = 'Failed to load products. Please try again.'
      console.error('Error fetching products:', err)
    }
  } finally {
    isLoading.value = false
  }
}

async function fetchCategories() {
  try {
    const res = await fetch('https://dummyjson.com/products/category-list')
    if (!res.ok) throw new Error('Failed to fetch categories')
    
    const data = await res.json()
    productCategoryList.value = data.map((item: string) => ({
      label: item.replace(/\b\w/g, char => char.toUpperCase()),
      value: item
    }))
  } catch (err) {
    console.error('Error fetching categories:', err)
  }
}

// Event Handlers
function handleSearchInput() {
  if (searchTimeout) {
    clearTimeout(searchTimeout)
  }
  
  searchTimeout = setTimeout(() => {
    resetPagination()
    selectedProductCategory.value = ''
    fetchProducts()
  }, DEBOUNCE_DELAY)
}

function handleImageError(event: Event) {
  const target = event.target as HTMLImageElement
  target.src = 'https://via.placeholder.com/400x400?text=No+Image'
}

function clearSearch() {
  searchProductKey.value = ''
  resetPagination()
  fetchProducts()
}

function clearCategory() {
  selectedProductCategory.value = ''
  resetPagination()
  fetchProducts()
}

function clearAllFilters() {
  searchProductKey.value = ''
  selectedProductCategory.value = ''
  sortOption.value = ''
  resetPagination()
  fetchProducts()
}

function resetPagination() {
  paginationData.value.skip = 0
}

function getCategoryLabel(value: string): string {
  const category = productCategoryList.value.find(cat => cat.value === value)
  return category ? category.label : value
}

function previousPage() {
  if (currentPage.value > 1) {
    paginationData.value.skip -= paginationData.value.limit
    fetchProducts()
    scrollToTop()
  }
}

function nextPage() {
  if (currentPage.value < getTotalPages.value) {
    paginationData.value.skip += paginationData.value.limit
    fetchProducts()
    scrollToTop()
  }
}

function goToPage(page: number) {
  if (page !== currentPage.value) {
    paginationData.value.skip = (page - 1) * paginationData.value.limit
    fetchProducts()
    scrollToTop()
  }
}

function retryFetch() {
  fetchProducts()
}

function scrollToTop() {
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function handleScroll() {
  showBackToTop.value = window.scrollY > 500
}

// Watchers
watch(selectedProductCategory, () => {
  if (selectedProductCategory.value) {
    searchProductKey.value = ''
    resetPagination()
    fetchProducts()
  }
})

// Lifecycle
onMounted(() => {
  fetchProducts()
  fetchCategories()
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
  if (searchTimeout) clearTimeout(searchTimeout)
  if (abortController) abortController.abort()
})
</script>

<style scoped>
.custom-scrollbar::-webkit-scrollbar {
  width: 6px;
}

.custom-scrollbar::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 10px;
}

.custom-scrollbar::-webkit-scrollbar-thumb {
  background: #c7d2fe;
  border-radius: 10px;
}

.custom-scrollbar::-webkit-scrollbar-thumb:hover {
  background: #a5b4fc;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>