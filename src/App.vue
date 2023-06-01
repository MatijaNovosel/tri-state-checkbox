<template>
  <tri-state-checkbox v-model="val" label="Vite + Vue" />
</template>

<script setup lang="ts">
import { ref } from "vue";
import triStateCheckbox from "./components/TriStateCheckbox.vue";

const val = ref(false);
</script>
