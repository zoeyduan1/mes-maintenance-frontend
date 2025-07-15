<template>
  <el-container style="height: 100vh">
    <!-- Left sidebar -->
    <el-aside width="300px" style="border-right: 1px solid #eee">
      <el-scrollbar>
        <LocationList :locations="locations" :selected="selected" @select="selectLocation" />
      </el-scrollbar>
    </el-aside>

    <!-- Right content -->
    <el-main>
      <LocationDetail :location="selected" />
    </el-main>
  </el-container>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { ElContainer, ElAside, ElMain, ElScrollbar } from 'element-plus'
import 'element-plus/dist/index.css'
import axios from 'axios'

import LocationList from './LocationList.vue'
import LocationDetail from './LocationDetail.vue'

// Data: vendor locations
const locations = ref([])

onMounted(async () => {
  const res = await axios.get('http://10.10.12.12:8085/location')
  locations.value = res.data?.data || []
})

// Selected location state
const selected = ref(null)

// Method: set selected item
const selectLocation = loc => {
  selected.value = loc
}
</script>
