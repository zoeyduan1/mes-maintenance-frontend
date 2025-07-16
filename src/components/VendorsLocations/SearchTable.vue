<template>
  <div>
    <el-table height="240" :data="paginatedData" style="width: 100%">
      <el-table-column label="Name" prop="name" />
      <el-table-column label="Code" prop="code" />
      <el-table-column label="Serial" prop="serial_number" />
      <el-table-column label="Group" prop="equipment_group" />
      <el-table-column label="Vendor" prop="vendor" />
      <el-table-column align="right">
        <template #header>
          <el-input v-model="search" size="medium" placeholder="Search by name" />
        </template>
      </el-table-column>
    </el-table>

    <div class="mt-4 flex justify-end">
      <el-pagination
        layout="prev, pager, next"
        :page-size="pageSize"
        :current-page="currentPage"
        :total="filteredEquipment.length"
        @current-change="handlePageChange"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed, defineProps } from 'vue'

const props = defineProps( {
  equipmentList : {
    type : Array,
    default : () => []
  }
} )

const search = ref( '' )
const currentPage = ref( 1 )
const pageSize = ref( 5 )

const filteredEquipment = computed( () =>
  props.equipmentList.filter( e => !search.value || e.name.toLowerCase().includes( search.value.toLowerCase() ) )
)

const paginatedData = computed( () => {
  const start = ( currentPage.value - 1 ) * pageSize.value
  return filteredEquipment.value.slice( start, start + pageSize.value )
} )

const handlePageChange = page => {
  currentPage.value = page
}
</script>
