<template>
  <div class="p-4">
    <h3 class="text-lg font-bold mb-4">Vendors & Locations</h3>

    <!-- Scrollable list of cards -->
    <el-scrollbar height="calc(100vh - 200px)">
      <template v-if="paginatedItems.length">
        <el-card
          v-for="item in paginatedItems"
          :key="item.id || item.name"
          class="mb-3 cursor-pointer hover:shadow-md focus:outline-none"
          :class="{ 'border-blue-500 border-2': props.selected?.name === item.name }"
          role="button"
          tabindex="0"
          @click="$emit('select', item)"
          @keyup.enter="$emit('select', item)"
        >
          <div>{{ item.name }}</div>
        </el-card>
      </template>
      <template v-else>
        <div class="text-gray-500 text-center mt-8">No locations found.</div>
      </template>
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
import { ref, computed, watch } from 'vue'
import { ElCard, ElScrollbar, ElPagination } from 'element-plus'

// Props
const props = defineProps( {
  locations : {
    type : Array,
    required : true
  },
  selected : Object
} )

defineEmits( ['select'] )

const currentPage = ref( 1 )
const pageSize = 5

const paginatedItems = computed( () =>
  props.locations.slice( ( currentPage.value - 1 ) * pageSize, currentPage.value * pageSize )
)

// Reset pagination when location list changes
watch( () => props.locations, () => {
  currentPage.value = 1
} )
</script>

<style scoped>
.el-card {
  min-height: 80px; /* Or adjust as needed */
  display: flex;
  flex-direction: column;
  justify-content: center; /* Vertically center content */
  transition: box-shadow 0.2s ease, border-color 0.2s ease, background-color 0.2s ease;
}

.el-card:focus {
  outline: none;
  background-color: #f0f9ff;   /* Very light blue */
}

.border-left-color-blue-500 {
  border-left-color: #3b82f6 !important;
}
</style>
