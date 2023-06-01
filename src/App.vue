<template>
  <vue-tri-state-checkbox v-model="val" label="Vite + Vue" />
</template>

<script setup lang="ts">
import { ref } from "vue";

const val = ref(false);
</script>
