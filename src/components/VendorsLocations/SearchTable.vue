<template>
  <el-table
    height="240"
    :data="filteredEquipment"
    style="width: 100%"
  >
    <el-table-column label="Name" prop="name" />
    <el-table-column label="Code" prop="code" />
    <el-table-column label="Serial" prop="serial_number" />
    <el-table-column label="Group" prop="equipment_group" />
    <el-table-column label="Vendor" prop="vendor" />
    <el-table-column align="right">
      <template #header>
        <el-input v-model="search" size="default" placeholder="Search by name" />
      </template>
    </el-table-column>
  </el-table>
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

const filteredEquipment = computed( () =>
  props.equipmentList.filter( e => !search.value || e.name.toLowerCase().includes( search.value.toLowerCase() ) )
)
</script>
