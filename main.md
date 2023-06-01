## Introduction

A situation that tends to happen when working on multiple projects is that the same generic components are needed on each individual project like a stylized text field or something a little bit more complex like a date picker.

The best course of action would be to write the component once and update it from a single point, that being the package where it's being distributed from.

What we'll be doing is writing an NPM package that exports a Vue 3 component with TypeScript type definitions to boot! The component I've chosen to make is a tri-state checkbox for this demonstration.

## Starting off

I'll be using Vite for this project since it is a nice alternative to webpack as a build tool and development server. Use your package manager of choice to create a project, I'll go with `yarn`:

`yarn create vite`

Choose these settings and name your project ideally how you would name your final component:

```
√ Project name: ... tri-state-checkbox
√ Select a framework: » Vue
√ Select a variant: » TypeScript
```

Purge the project of any files or code you don't need, I usually just use `App.vue` for testing purposes with the `components` folder being used to store the components and export them.

Your `src` folder should look like this:

</slika/>

## The component

Write your component as you would usually with regards to how Vue 3 works, I am going to use this as an example for my component:

```ts
<template>
  <label class="m-checkbox">
    <input
      type="checkbox"
      :disabled="disabled"
      :indeterminate="val === null"
      :checked="val === true"
      @click="change"
    />
    <span>
      {{ label }}
    </span>
  </label>
</template>

<script setup lang="ts">
import { ref, watch } from "vue";

const props = withDefaults(
  defineProps<{
    label?: string;
    modelValue: boolean | null;
    disabled?: boolean;
    color?: string;
  }>(),
  {
    color: "#2f4fef"
  }
);

const emit = defineEmits<{
  (e: "update:modelValue", value: boolean | null): void;
}>();

const val = ref<boolean | null>(false);

const change = () => {
  if (val.value === false) val.value = null;
  else if (val.value === null) val.value = true;
  else val.value = false;
  emit("update:modelValue", val.value);
};

watch(
  () => props.modelValue,
  (value) => (val.value = value)
);
</script>
```

## package.json

We will need to alter `package.json` in order to distribute the newly created component (the words component and package will be used interchangeably from now on):

- `private` needs to be set to `false`
- add a `files` property to specify which files will play a role in how the package functions, after building the component with `yarn build` all of the distribution files will be inside the `dist` folder

```
"files": ["dist", "src/components/"],
```

Additionally, we will be adding the `src/components` folder because the components are exported from there.
