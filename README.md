<div align="center">
  <img src="https://github.com/MatijaNovosel/tri-state-checkbox/assets/36193643/0d2f440b-7057-418a-b6e0-9be0513a3e4d" />
</div>

<h1 align=center>Vue tri state checkbox</h1>
<p align=center>A material tri state checkbox component for Vue 3.</p>

## 🚀 Installation

Install using your package manager of choice:

```bash
yarn add vue-tri-state-checkbox
```

## ⚙️ Usage

Import the component locally or define it globally and include the css file:

```vue
<template>
  <tri-state-checkbox v-model="val" />
</template>

<script lang="ts" setup>
import { ref } from "vue";
import { TriStateCheckbox } from "vue-tri-state-checkbox";
import "vue-tri-state-checkbox/dist/style.css";
const val = ref(null);
</script>
```

## 📃 Props

| Name       | Type           | Default | Description                        |
| ---------- | -------------- | ------- | ---------------------------------- |
| `v-model`  | `boolean/null` |         | Standard two way input             |
| `disabled` | `boolean`      | false   | Makes the component uninteractable |
| `color`    | `string`       | #3ba13b | Color of the checkbox background   |
