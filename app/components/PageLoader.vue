<template>
  <div class="flex items-center justify-center h-full w-full">
    <UButton
      :loading="loading"
      variant="ghost"
      class="outline-none ring-0 shadow-none focus:outline-none focus:ring-0 border-none p-0 text-gray-1000"
    />
  </div>
</template>

<script setup lang="ts">
const props = defineProps({
  loading: {
    type: Boolean,
    default: true
  }
})
</script>
