<template>
  <v-app class="body">
    <v-main>
      <Weather />
    </v-main>
  </v-app>
</template>

<script setup>
  import Weather from '@/components/Weather.vue';
</script>

<style scoped>
.body{
  background-color: #455A64;
  color: white;
}
</style>