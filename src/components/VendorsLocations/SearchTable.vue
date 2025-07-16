<template>
  <el-table height="240" :data="filteredData" style="width: 100%"   empty-text="No data found">
    <el-table-column v-for="col in columns" :key="col.prop" :label="col.label" :prop="col.prop" />
    <!-- Optional search column -->
    <el-table-column v-if="enableSearch" align="right">
      <template #header>
        <el-input v-model="search" size="default" placeholder="Search" clearable />
      </template>
    </el-table-column>
  </el-table>
</template>

<script setup>
import { ref, computed, defineProps } from 'vue'

const props = defineProps( {
  data : {
    type : Array,
    default : () => []
  },
  columns : {
    type : Array,
    default : () => []
  },
  enableSearch : {
    type : Boolean,
    default : true
  }
} )

const search = ref( '' )

// Automatically expose search value to parent if needed via emit, or manage internally
const filteredData = computed( () => {
  const query = search.value.toLowerCase()
  return props.data.filter( row => !query || Object.values( row ).some( val => String( val ).toLowerCase().includes( query ) ) )
} )
</script>
