<script setup>
// import { RouterLink, RouterView } from "vue-router";
import AppLayout from "./components/AppLayout.vue";
</script>

<template>
  <component :is="AppLayout">
    <router-view v-slot="{ Component }">
      <component :is="Component" />
    </router-view>
  </component>
</template>
