<script setup>
// import { useRoute } from "vue-router";
import LayoutHeader from "@/components/layout/LayoutHeader.vue";
import LayoutFooter from "@/components/layout/LayoutFooter.vue";

// const route = useRoute();
</script>
<template>
  <div class="layout min-h-screen w-screen bg-green">
    <LayoutHeader />

    <div class="slot min-h-full w-full">
      <slot />
    </div>
    <LayoutFooter />
  </div>
</template>
