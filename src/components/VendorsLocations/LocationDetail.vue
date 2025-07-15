<template>
  <el-card class="mb-4" v-if="location">
    <div class="el-card__header">
      <span class="el-card__title">Location Overview</span>
      <el-button type="primary" icon="el-icon-edit" size="small" @click="editLocation = true"> Edit </el-button>
    </div>
    <el-row :gutter="20">
      <el-col :span="12">
        <div class="label">Name</div>
        <div class="value">{{ location.name }}</div>
      </el-col>
      <el-col :span="12">
        <div class="label">Code</div>
        <div class="value">{{ location.code }}</div>
      </el-col>
      <el-col :span="12">
        <div class="label">Address</div>
        <div class="value">{{ location.address }}</div>
      </el-col>
      <el-col :span="12">
        <div class="label">Location Type</div>
        <div class="value">{{ location.location_type?.name }}</div>
      </el-col>
      <el-col :span="12">
        <div class="label">Person in Charge</div>
        <div class="value">{{ location.person_in_charge_id }}</div>
      </el-col>
      <el-col :span="24">
        <div class="label">Description</div>
        <div class="value">{{ location.description || '--' }}</div>
      </el-col>
    </el-row>
    <el-divider v-if="location?.image_path?.length">Images</el-divider>
    <Images v-if="location?.image_path?.length" :images="location.image_path" />
  </el-card>
</template>

<script setup>
import { ref } from 'vue'
import Images from './Images.vue'
defineProps({
  location: Object,
})

const editLocation = ref(false)
</script>

<style scoped>
.label {
  font-size: 12px;
  color: #999;
  margin-bottom: 4px;
}
.value {
  font-size: 14px;
  font-weight: 500;
  color: #333;
  margin-bottom: 16px;
}
.el-card__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.el-card__title {
  font-size: 18px;
  font-weight: bold;
}
</style>
