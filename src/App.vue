<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SiteNavigation/>
    <router-view></router-view>
  </div>
</template>

<script setup>
import {RouterView} from 'vue-router'
import SiteNavigation from "./components/SiteNavigation.vue";
</script>

<style scoped></style>
