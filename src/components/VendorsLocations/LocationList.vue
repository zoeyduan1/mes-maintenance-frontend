<template>
  <div class="p-4">
    <h3 class="text-lg font-bold mb-4">Vendors & Locations</h3>

    <!-- Scrollable list of cards -->
    <el-scrollbar height="calc(100vh - 160px)">
      <el-card
        v-for="(item, index) in paginatedItems"
        :key="index"
        class="mb-3 cursor-pointer hover:shadow-md"
        :class="{ 'border-blue-500 border-2': props.selected?.name === item.name }"
        @click="$emit('select', item)"
      >
        <div class="font-semibold">{{ item.name }}</div>
        <div class="text-sm text-gray-600">{{ item.address }}</div>
        <div class="text-xs text-gray-400 mt-1">Type: {{ item.type }}</div>
      </el-card>
    </el-scrollbar>

    <!-- Pagination controls -->
    <div class="mt-4 text-center">
      <el-pagination
        background
        layout="prev, pager, next"
        :page-size="pageSize"
        :total="props.locations.length"
        v-model:current-page="currentPage"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { ElCard, ElScrollbar, ElPagination } from 'element-plus'

// Use props as a reactive object (do NOT destructure to avoid reactivity loss)
const props = defineProps({
  locations: {
    type: Array,
    required: true,
  },
  selected: Object,
})

defineEmits(['select'])

const currentPage = ref(1)
const pageSize = 5

const paginatedItems = computed(() =>
  props.locations.slice((currentPage.value - 1) * pageSize, currentPage.value * pageSize)
)
</script>

<style scoped>
.el-card {
  transition: box-shadow 0.2s ease;
}
</style>
