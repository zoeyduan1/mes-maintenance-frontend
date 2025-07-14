<template>
  <div class="p-4">
    <h3 class="text-lg font-bold mb-2">Location Detail</h3>
    <div v-if="location">
      <p><strong>Name:</strong> {{ location.name }}</p>
      <p><strong>Address:</strong> {{ location.address }}</p>
      <p><strong>Type:</strong> {{ location.type || '--' }}</p>
    </div>
    <div v-else>
      <p>Please select a location from the left.</p>
    </div>
  </div>
</template>

<script setup>
defineProps( {
  location : Object
} )
</script>
