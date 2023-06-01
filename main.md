## Introduction

A situation that tends to happen when working on multiple projects is that the same generic components are needed on each individual project like a stylized text field or something a little bit more complex like a date picker.

The best course of action would be to write the component once and update it from a single point, that being the package where it's being distributed from.

What we'll be doing is writing an NPM package that exports a Vue 3 component with TypeScript type definitions to boot! The component I've chosen to make is a tri-state checkbox for this demonstration.

## Starting off

I'll be using Vite for this project since it is a nice alternative to webpack as a build tool and development server written by Evan You himself.

Use your package manager of choice to create a project, I'll go with `yarn`:

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

## Writing the component

Write your component as you would usually with regards to how Vue 3 works, I am going to use this as an example for my component:

```typescript
<template>
  <div>

  </div>
</template>

<script lang="ts" setup>

</setup>
```

I added sass as well since Vite supports it out of the box and it makes writing css more convenient: `yarn add sass`
