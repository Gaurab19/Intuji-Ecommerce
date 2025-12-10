<template>
  <div
    class="p-4 mx-auto lg:max-w-screen-xl"
  >
    <div class="border-b border-gray-300 pb-4">
      <h2 class="text-slate-900 text-2xl font-bold">
        Hot list
      </h2>
      <p class="text-slate-600 mt-2">
        Out the most popular and trending products.
      </p>
    </div>

    <PageLoader
      v-if="isLoading"
      :loading="isLoading"
    />
    <div v-else>
      <div class="flex">
        <div class="w-full max-w-[300px] shrink-0 shadow-md px-6 sm:px-8 min-h-screen py-6">
          <div class="flex items-center border-b border-gray-300 pb-2 mb-6">
            <h3 class="text-slate-900 text-lg font-semibold">
              Filter
            </h3>
          </div>
          <div>
            <h6 class="text-slate-900 text-sm font-semibold">
              Search For Products
            </h6>
            <div class="flex px-3 py-1.5 rounded-md  overflow-hidden mt-2">
              <UInput
                ref="input"
                v-model="searchProductKey"
                placeholder="Search for Your Desired Product"
                @keyup="processChange"
              >
                <template #trailing />
              </UInput>
            </div>
          </div>

          <hr class="my-6 border-gray-300">

          <hr class="my-6 border-gray-300">

          <div>
            <h6 class="text-slate-900 text-sm font-semibold">
              Price
            </h6>
            <div class="relative mt-6">
              <div class="h-1.5 bg-gray-300 relative">
                <USlider
                  v-model="priceSliderValue"
                  :min="0"
                  :max="150"
                  :default-value="10"
                  :step="1.5"
                />
              </div>
            </div>
          </div>
        </div>

        <div class="w-full p-6">
          <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 sm:gap-12 md:gap-12 mt-12 mb-10">
            <div
              v-for="product in products"
              key="product"
              class="flex flex-col border border-gray-300 shadow-sm rounded-md p-1.5 transition-all relative overflow-hidden"
            >
              <NuxtLink :to="`/${product.id}`">
                <a
                  href="javascript:void(0)"
                  class="block"
                >
                  <div class="w-full bg-slate-50">
                    <img
                      :src="product.images[0]"
                      alt="Product-1"
                      class="w-full aspect-square object-cover object-top"
                    >
                  </div>
                  <div class="py-4 px-2 text-left">
                    <h6 class="text-sm sm:text-[15px] font-semibold text-slate-900 line-clamp-2">{{ product.title }}</h6>
                    <p class="text-sm mt-1 text-slate-600 truncate">{{ product.description }}</p>
                    <p class="text-sm mt-1 text-red-600 truncate">{{ product.brand }}</p>
                    <div class="flex items-center gap-1 mt-2">
                      <svg
                        v-for="n in Math.floor(product.rating)"
                        :key="`full-${n}`"
                        xmlns="http://www.w3.org/2000/svg"
                        class="w-3 h-3 sm:w-3.5 sm:h-3.5 shrink-0"
                        width="14"
                        height="14"
                        viewBox="0 0 511.987 511"
                      >
                        <path
                          fill="#ffc107"
                          d="M510.652 195.902a27.158 27.158 0 0 0-23.425-18.71l-147.774-13.419-58.433-136.77c-4.31-10.023-14.122-16.509-25.024-16.509s-20.715 6.487-25.023 16.534l-58.434 136.746-147.797 13.418a27.208 27.208 0 0 0-23.402 18.71c-3.371 10.368-.258 21.739 7.957 28.907l111.7 97.96-32.938 145.09c-2.41 10.668 1.73 21.696 10.582 28.094 4.757 3.438 10.324 5.188 15.937 5.188 4.84 0 9.64-1.305 13.95-3.883l127.468-76.184 127.422 76.184c9.324 5.61 21.078 5.097 29.91-1.305a27.223 27.223 0 0 0 10.582-28.094l-32.937-145.09 111.699-97.94a27.224 27.224 0 0 0 7.98-28.927zm0 0"
                          data-original="#ffc107"
                        />
                      </svg>
                      <!-- for-half-stars v-if="item.ranking % 1 !== 0" -->
                      <svg
                        v-for="n in 5 - Math.floor(product.rating)"
                        :key="`empty-${n}`"
                        xmlns="http://www.w3.org/2000/svg"
                        class="w-3 h-3 sm:w-3.5 sm:h-3.5 shrink-0"
                        width="14"
                        height="14"
                        fill="#ffc107"
                        viewBox="0 0 511.987 511"
                      >
                        <path
                          d="M114.594 501.14c-5.61 0-11.18-1.75-15.934-5.187a27.223 27.223 0 0 1-10.582-28.094l32.938-145.09L9.312 224.81a27.188 27.188 0 0 1-7.976-28.907 27.208 27.208 0 0 1 23.402-18.71l147.797-13.419L230.97 27.027c4.307-10.047 14.119-16.535 25.022-16.535s20.715 6.488 25.024 16.512l58.433 136.77 147.774 13.417c10.882.98 20.054 8.344 23.425 18.711 3.372 10.368.254 21.739-7.957 28.907L390.988 322.75l32.938 145.086c2.414 10.668-1.727 21.7-10.578 28.098-8.832 6.398-20.61 6.89-29.91 1.3l-127.446-76.16-127.445 76.203c-4.309 2.559-9.11 3.864-13.953 3.864zm141.398-112.874c4.844 0 9.64 1.3 13.953 3.859l120.278 71.938-31.086-136.942a27.21 27.21 0 0 1 8.62-26.516l105.473-92.5-139.543-12.671a27.18 27.18 0 0 1-22.613-16.493L255.992 49.895 200.844 178.96c-3.883 9.195-12.524 15.512-22.547 16.43L38.734 208.062l105.47 92.5c7.554 6.614 10.858 16.77 8.62 26.54l-31.062 136.937 120.277-71.914c4.309-2.559 9.11-3.86 13.953-3.86zm-84.586-221.848s0 .023-.023.043zm169.13-.063.023.043c0-.023 0-.023-.024-.043zm0 0"
                          data-original="#000000"
                        />
                      </svg>
                      <span class="ml-1.5 text-sm font-medium">({{ product?.reviews?.length }})</span>
                    </div>
                    <div class="mt-4">
                      <p class="text-slate-900 font-semibold text-sm sm:text-[15px] break-words">
                        <span class="mr-1.5">MRP:</span><strike class="mr-1.5 text-slate-600">{{ `${(product.price + ((product.discountPercentage*product.price)/100)).toFixed(2)}` }}</strike>{{ product.price }}</p>
                    </div>
                  </div>
                </a>
              </NuxtLink>
              <!-- <div class="h-[78px]">
            <div class="flex flex-col items-center w-full absolute left-0 right-0 px-2 bottom-3">
              <button
                type="button"
                class="flex items-center justify-center gap-2 px-2 py-2 cursor-pointer rounded-md text-white text-sm sm:text-[15px] font-medium whitespace-nowrap border-0 outline-0 w-full bg-purple-700 hover:bg-purple-800"
              >
                Add to Cart
              </button>
              <button
                type="button"
                class="pb-0.5 pt-3 cursor-pointer text-sm sm:text-[15px] text-slate-900 font-medium whitespace-nowrap outline-0 flex items-center gap-2"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="w-4 h-4"
                  width="16px"
                  height="16px"
                  viewBox="0 0 66 66"
                ><path
                  d="M45.5 4A18.53 18.53 0 0 0 32 9.86 18.5 18.5 0 0 0 0 22.5C0 40.92 29.71 59 31 59.71a2 2 0 0 0 2.06 0C34.29 59 64 40.92 64 22.5A18.52 18.52 0 0 0 45.5 4ZM32 55.64C26.83 52.34 4 36.92 4 22.5a14.5 14.5 0 0 1 26.36-8.33 2 2 0 0 0 3.27 0A14.5 14.5 0 0 1 60 22.5c0 14.41-22.83 29.83-28 33.14Z"
                  data-original="#000000"
                />
                </svg>
                <span>Add to wishlist</span>
              </button>
            </div>
          </div> -->
            </div>
          </div>

          <div
            v-if="paginationData.total>0"
            class="flex items-center justify-between border-t border-gray-200 bg-white px-4 py-3 sm:px-6"
          >
            <div class="flex flex-1 justify-between sm:hidden">
              <a
                href="#"
                class="relative inline-flex items-center rounded-md border border-gray-300 bg-white px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50"
              >Previous</a>
              <a
                href="#"
                class="relative ml-3 inline-flex items-center rounded-md border border-gray-300 bg-white px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50"
              >Next</a>
            </div>
            <div class="hidden sm:flex sm:flex-1 sm:items-center sm:justify-between">
              <div>
                <p class="text-sm text-gray-700">
                  Showing
                  <span class="font-medium">{{ paginationData?.skip + 1 }}</span>
                  to
                  <span class="font-medium">{{ paginationData?.limit }}</span>
                  of
                  <span class="font-medium">{{ paginationData?.total }}</span>
                  results
                </p>
              </div>
              <div>
                <nav
                  aria-label="Pagination"
                  class="isolate inline-flex -space-x-px rounded-md shadow-xs"
                >
                  <a
                    href="#"
                    class="relative inline-flex items-center rounded-l-md px-2 py-2 text-gray-400 inset-ring inset-ring-gray-300 hover:bg-gray-50 focus:z-20 focus:outline-offset-0"
                  >
                    <span class="sr-only">Previous</span>
                    <svg
                      viewBox="0 0 20 20"
                      fill="currentColor"
                      data-slot="icon"
                      aria-hidden="true"
                      class="size-5"
                    >
                      <path
                        d="M11.78 5.22a.75.75 0 0 1 0 1.06L8.06 10l3.72 3.72a.75.75 0 1 1-1.06 1.06l-4.25-4.25a.75.75 0 0 1 0-1.06l4.25-4.25a.75.75 0 0 1 1.06 0Z"
                        clip-rule="evenodd"
                        fill-rule="evenodd"
                      />
                    </svg>
                  </a>
                  <!-- Current: "z-10 bg-indigo-600 text-white focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600", Default: "text-gray-900 inset-ring inset-ring-gray-300 hover:bg-gray-50 focus:outline-offset-0" -->
                  <a
                    v-for="item in pageList"
                    :key="item"
                    href="#"
                    :class="{
                      'bg-indigo-600 dark:text-blue-200': item === paginationData.skip+1
                    }"
                    class="relative z-10 inline-flex bg-indigo-200 items-center px-4 py-2 text-sm bg-white-600 font-semibold text-white focus:z-20 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
                  >{{ item }}</a>
                  <a
                    href="#"
                    class="relative inline-flex items-center rounded-r-md px-2 py-2 text-gray-400 inset-ring inset-ring-gray-300 hover:bg-gray-50 focus:z-20 focus:outline-offset-0"
                  >
                    <span class="sr-only">Next</span>
                    <svg
                      viewBox="0 0 20 20"
                      fill="currentColor"
                      data-slot="icon"
                      aria-hidden="true"
                      class="size-5"
                    >
                      <path
                        d="M8.22 5.22a.75.75 0 0 1 1.06 0l4.25 4.25a.75.75 0 0 1 0 1.06l-4.25 4.25a.75.75 0 0 1-1.06-1.06L11.94 10 8.22 6.28a.75.75 0 0 1 0-1.06Z"
                        clip-rule="evenodd"
                        fill-rule="evenodd"
                      />
                    </svg>
                  </a>
                </nav>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const toast = useToast()

const paginationData = ref({
  limit: 30,
  skip: 0,
  total: null
})
const products = ref([])
const pageList = ref([])
const productCategoryList = ref([])
const selectedProductCategory = ref('')
const isLoading = ref(true)
const searchProductKey = ref('')
const priceSliderValue = ref(null)
async function fetchInitialData() {
  const res = await $fetch(`https://dummyjson.com/products?limit=${paginationData.value.limit}&skip=${paginationData.value.skip}`, {
    method: 'GET'
  })
  paginationData.value = {
    limit: res?.limit,
    skip: res?.skip,
    total: res?.total
  }
  products.value = res.products
  getPagesList()
  isLoading.value = false
}
fetchInitialData()

async function fetchProductCategories() {
  await $fetch('https://dummyjson.com/products/category-list')
    .then((res) => {
      productCategoryList.value = res
    })
}
fetchProductCategories()

const getTotalPages = computed(() => {
  return Math.ceil(paginationData.value.total / paginationData.value.limit)
})
const getPagesList = () => {
  for (let index = 1; index <= getTotalPages.value; index++) {
    pageList.value.push(index)
  }
}
function debounce(func, timeout = 500) {
  let timer
  return (...args) => {
    clearTimeout(timer)
    timer = setTimeout(() => { func.apply(this, args) }, timeout)
  }
}
async function saveInput() {
  const res = await $fetch(`https://dummyjson.com/products/search?q=${searchProductKey.value}`, {
    method: 'GET'
  })
  if (res.total == 0) {
    window.alert(`Item ${searchProductKey.value} isn't available at the moment!`)
    paginationData.value.total = 0
  }
  products.value = res.products
  paginationData.value.total = res.total
}
const processChange = debounce(() => saveInput())
watch(priceSliderValue, async (newValue, oldValue) => {
  if (newValue) {
    // const res = await $fetch(`https://dummyjson.com/products?select=price`, {
    //   method: 'GET'
    // })
    const res = await $fetch('https://dummyjson.com/products')
      .then((res) => {
        const filtered = res.products.filter(p => p.price < newValue)
        if (filtered.length == 0) {
          window.alert(`Item ${searchProductKey.value} isn't available at the moment!`)
          paginationData.value.total = 0
        }
        products.value = filtered
        // paginationData.value.total = filtered.length
      })
  }
})
</script>
