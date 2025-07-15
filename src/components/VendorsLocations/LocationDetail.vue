<template>
  <el-card class="mb-4" v-if="location">
    <template #header>
      <div class="el-card__header">
        <span class="el-card__title">{{ location.name }}</span>
        <div class="button-group">
          <el-button type="primary" :icon="Edit" circle @click="editLocation = true" />
          <el-button type="danger" :icon="Delete" circle />
        </div>
      </div>
    </template>

    <div class="descriptions-container">
      <el-descriptions
          title="Overview"
          :column="2"
          direction="vertical"
      >
        <el-descriptions-item label="Name">{{ location.name }}</el-descriptions-item>
        <el-descriptions-item label="Code">{{ location.code }}</el-descriptions-item>
        <el-descriptions-item label="Location Type">{{ location.location_type?.name }}</el-descriptions-item>
        <el-descriptions-item label="Person in Charge">{{ location.person_in_charge_id }}</el-descriptions-item>
        <el-descriptions-item label="Address">{{ location.address }}</el-descriptions-item>
      </el-descriptions>

      <el-descriptions
          v-if="location.description"
          :column="1"
          direction="vertical"
          :style="blockMargin"
      >
        <el-descriptions-item label="Description">{{ location.description }}</el-descriptions-item>
      </el-descriptions>

      <div v-if="location?.image_path?.length">
        <el-descriptions title="Images"></el-descriptions>
        <Images :images="location.image_path" />
      </div>
    </div>
  </el-card>
</template>

<script setup>
import { ref } from 'vue'
import {
  ElCard,
  ElDescriptions,
  ElDescriptionsItem,
  ElButton
} from 'element-plus'
import { Edit, Delete } from '@element-plus/icons-vue'
import Images from './Images.vue'

defineProps( {
  location : Object
} )

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
.button-group {
  display: flex;
  gap: 8px;
}
.descriptions-container {
  max-width: 800px;
  width: 100%;
  margin-left: 0;
}

</style>
