<template>
  <div class="p-4">
    <div v-if="location">
      <p><strong>Name:</strong> {{ location.name }}</p>
      <p><strong>Code:</strong> {{ location.code }}</p>
      <p><strong>Address:</strong> {{ location.address }}</p>
      <p><strong>Location Type:</strong> {{ location.location_type.name }}</p>
      <p><strong>Location Type:</strong> {{ location.person_in_charge_id }}</p>
      <p><strong>Description:</strong> {{ location.description || '--' }}</p>
      <p><strong>Images:</strong></p>
      <Images :images="location.image_path" /></div>
  </div>
</template>

<script setup>
import Images from './Images.vue'
defineProps( {
  location : Object
} )
</script>
