<template>
  <el-card class="mb-4" v-if="location">
    <template #header>
      <div class="el-card__header">
        <span class="el-card__title">
          <template v-if="editLocation">
            <el-input v-model="editForm.name" size="small" />
          </template>
          <template v-else>
            {{ location.name }}
          </template>
        </span>

        <el-button-group class="ml-4" size="default">
          <el-button v-if="!editLocation" type="primary" :icon="Edit" @click="enterEditMode" />
          <el-button v-else type="success" @click="saveEdit"> Save </el-button>
          <el-button type="primary" :icon="Share" />
          <el-button type="primary" :icon="Delete" />
        </el-button-group>
      </div>
    </template>

    <div class="descriptions-container">
      <el-descriptions title="Overview" :column="2" direction="vertical">
        <el-descriptions-item label="Name">
          <template v-if="editLocation">
            <el-input v-model="editForm.name" size="small" />
          </template>
          <template v-else>
            {{ location.name }}
          </template>
        </el-descriptions-item>

        <el-descriptions-item label="Code">
          <template v-if="editLocation">
            <el-input v-model="editForm.code" size="small" />
          </template>
          <template v-else>
            {{ location.code }}
          </template>
        </el-descriptions-item>

        <el-descriptions-item label="Location Type">
          <template v-if="editLocation">
            <el-input v-model="editForm.location_type.name" size="small" />
          </template>
          <template v-else>
            {{ location.location_type?.name }}
          </template>
        </el-descriptions-item>

        <el-descriptions-item label="Person in Charge">
          <template v-if="editLocation">
            <el-input v-model="editForm.person_in_charge_id" size="small" />
          </template>
          <template v-else>
            {{ location.person_in_charge_id }}
          </template>
        </el-descriptions-item>

        <el-descriptions-item label="Address">
          <template v-if="editLocation">
            <el-input v-model="editForm.address" size="small" />
          </template>
          <template v-else>
            {{ location.address }}
          </template>
        </el-descriptions-item>
      </el-descriptions>

      <el-descriptions v-if="location.description" :column="1" direction="vertical" :style="blockMargin">
        <el-descriptions-item label="Description">
          <template v-if="editLocation">
            <el-input type="textarea" v-model="editForm.description" size="small" />
          </template>
          <template v-else>
            {{ location.description }}
          </template>
        </el-descriptions-item>
      </el-descriptions>

      <div v-if="location?.image_path?.length">
        <el-descriptions title="Images"></el-descriptions>
        <Images :images="location.image_path" />
      </div>

      <div v-if="equipmentList?.length" style="margin-bottom: 48px">
        <el-descriptions title="Related Equipment"></el-descriptions>
        <SearchTable
          :data="equipmentList"
          :columns="[
            { label: 'Name', prop: 'name' },
            { label: 'Code', prop: 'code' },
            { label: 'Group', prop: 'equipment_group' },
          ]"
        />
      </div>

      <div v-if="sparePartsBatchList?.length">
        <el-descriptions title="Related Parts Batches"></el-descriptions>
        <SearchTable
          :data="sparePartsBatchList"
          :columns="[
            { label: 'Name', prop: 'name' },
            { label: 'Part Code', prop: 'code' },
            { label: 'Quantity', prop: 'quantity' },
          ]"
        />
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

const props = defineProps({
  location: Object,
})

const editLocation = ref(false)
const editForm = ref({})

// Create a copy of the location when entering edit mode
const enterEditMode = () => {
  editForm.value = JSON.parse(JSON.stringify(props.location))
  editLocation.value = true
}

const saveEdit = () => {
  console.log('Saving edited form:', editForm.value)
  // TODO: send to API or emit event
  editLocation.value = false
}

const equipmentList = ref([])
const sparePartsBatchList = ref([])

const fetchEquipment = async id => {
  try {
    const res = await axios.get(`http://10.10.12.12:8085/location/correlative-equipment/${id}`)
    equipmentList.value = res.data?.data || []
  } catch (err) {
    console.error('Failed to fetch equipment:', err)
    equipmentList.value = []
  }
}

const fetchsparePartsBatch = async id => {
  try {
    const res = await axios.get(`http://10.10.12.12:8085/location/correlative-spare-part-batch/${id}`)
    sparePartsBatchList.value = res.data?.data || []
  } catch (err) {
    console.error('Failed to fetch spare parts batch:', err)
    sparePartsBatchList.value = []
  }
}

watch(
  () => props.location?.id,
  id => {
    if (id) {
      fetchEquipment(id)
      fetchsparePartsBatch(id)
    }
  },
  { immediate: true }
)

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
