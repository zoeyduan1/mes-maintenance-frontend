<template>
  <el-card class="mb-4" v-if="location">
    <template #header>
      <div class="el-card__header">
        <span class="el-card__title">{{ location.name }}</span>
        <el-button-group class="ml-4" size="medium">
          <el-button type="primary" :icon="Edit" @click="editLocation = true"/>
          <el-button type="primary" :icon="Share" />
          <el-button type="primary" :icon="Delete" />
        </el-button-group>
      </div>
    </template>

    <div class="descriptions-container">
      <el-descriptions title="Overview" :column="2" direction="vertical">
        <el-descriptions-item label="Name">{{ location.name }}</el-descriptions-item>
        <el-descriptions-item label="Code">{{ location.code }}</el-descriptions-item>
        <el-descriptions-item label="Location Type">{{ location.location_type?.name }}</el-descriptions-item>
        <el-descriptions-item label="Person in Charge">{{ location.person_in_charge_id }}</el-descriptions-item>
        <el-descriptions-item label="Address">{{ location.address }}</el-descriptions-item>
      </el-descriptions>

      <el-descriptions v-if="location.description" :column="1" direction="vertical" :style="blockMargin">
        <el-descriptions-item label="Description">{{ location.description }}</el-descriptions-item>
      </el-descriptions>

      <div v-if="location?.image_path?.length">
        <el-descriptions title="Images"></el-descriptions>
        <Images :images="location.image_path" />
      </div>
      <div v-if="equipmentList?.length">
        <el-descriptions title="Related Equipment"></el-descriptions>
        <SearchTable :equipmentList="equipmentList" />
      </div>
    </div>
  </el-card>
</template>

<script setup>
import { ref, watch } from 'vue'
import axios from 'axios'
import SearchTable from '@/components/VendorsLocations/SearchTable.vue'
import { Delete, Edit, Share } from '@element-plus/icons-vue'
import Images from './Images.vue'

const props = defineProps( {
  location : Object
} )

const equipmentList = ref( [] )

const fetchEquipment = async id => {
  try {
    const res = await axios.get( `http://10.10.12.12:8085/location/correlative-equipment/${id}` )
    equipmentList.value = res.data?.data || []
  } catch ( err ) {
    console.error( 'Failed to fetch equipment:', err )
    equipmentList.value = []
  }
}

watch(
  () => props.location?.id,
  id => {
    if ( id ) fetchEquipment( id )
  },
  { immediate : true }
)

const editLocation = ref( false )
const blockMargin = 'margin-bottom: 32px'
</script>

<style scoped>
.el-card__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.el-card__title {
  font-size: 18px;
  font-weight: bold;
}
.descriptions-container {
  max-width: 1200px;
  width: 100%;
  margin-left: 0;
}
</style>
