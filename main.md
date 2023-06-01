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
  <label>
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

## Making the component exportable

Inside of your `src` folder we will create an `index.ts` file which will be used for exporting the component from the `components` folder.

```ts
import TriStateCheckbox from "./components/triStateCheckbox.vue";
export { TriStateCheckbox };
```

We will need some new dependencies for the next step, so add these packages to your development dependencies which will be explained shortly:

`yarn add vite-plugin-dts path -D`

The default `vite.config.ts` you should have at this point resembles this:

```ts
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()]
});
```

We'll be adding a few more lines to it, as so:

```ts
import vue from "@vitejs/plugin-vue";
import * as path from "path";
import { defineConfig } from "vite";
import dts from "vite-plugin-dts";

export default defineConfig({
  plugins: [vue(), dts()],
  build: {
    lib: {
      entry: path.resolve(__dirname, "src/index.ts"),
      name: "TriStateCheckbox",
      fileName: "tri-state-checkbox"
    },
    rollupOptions: {
      external: ["vue"],
      output: {
        globals: {
          vue: "Vue"
        }
      }
    }
  },
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "src")
    }
  }
});
```

Let's go over what's going on here: first off, inside the `plugins` property we added `dts` which is part of the `vite-plugin-dts` package. Its main purpose is creating type definition files from vue files automatically for better development experience, that is to say it create `d.ts` files from `.vue` ones.

Inside the `build` property we have defined some additional properties which will be used for defining how the build process of the package itself should work.

`lib` tells the build process to treat the entire thing like an exportable library, read more [here](https://vitejs.dev/config/build-options.html#build-lib). The properties we define here are:

- `build` - where the package export is, that is to say where the user will import the package from after publishing it, here we point the entry to the previously defined `index.ts` folder inside `src` using the `path` function and the `__dirname` variable which points to the main folder of the project
- `name` - a unique identifier of the package, try to keep this in line with your package name
- `fileName` - a unique identifier which will be used to describe the package file output

Inside `rollupOptions` we will define a few things to tell Rollup how to configure the module bundler:

- **external** - a definition of any external dependencides needed to run the package, in this case `vue` itself, read more [here](https://rollupjs.org/configuration-options/#external)
- **output.globals** - needed to provide context to the used external dependencies, read more [here](https://rollupjs.org/configuration-options/#output-globals)

Next we'll be editing the `tsconfig.json` file:

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "strict": true,
    "jsx": "preserve",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "lib": ["ESNext", "DOM"],
    "skipLibCheck": true,
    "noEmit": true
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

Notable changes here are to the `target` property and generally the transition from `ES2020` which is the default to `ESNext`.

## Building the package

When you're all done editing the previously mentioned files, we need to see if the build process works by running `yarn build`.

You should get an output similar to this:

```
$ vue-tsc && vite build
vite v4.3.9 building for production...
✓ 4 modules transformed.
dist/style.css              2.15 kB │ gzip: 0.65 kB
dist/tri-state-checkbox.js  1.12 kB │ gzip: 0.58 kB
dist/tri-state-checkbox.umd.cjs  1.09 kB │ gzip: 0.61 kB

[vite:dts] Start generate declaration files...
✓ built in 1.29s
[vite:dts] Declaration files built in 822ms.

Done in 3.49s.
```

There are 3 generated files, possibly even more if you added more dependencies or split your component into multiple files. These being:

- `style.css` - the CSS of the package bundled inside a singular entry point
- `tri-state-checkbox.js` - main package entry point which contains the bundled JavaScript code
- `tri-state-checkbox.umd.cjs` - universal module system and common js entry point

All of these are located inside the `dist` folder which will be used to distribute the package with NPM. Before we do that though, we have to edit `package.json`.

## package.json

We will need to alter `package.json` in order to distribute the newly created component (the words component and package will be used interchangeably from now on):

- `private` needs to be set to `false`
- add a `files` property to specify which files will play a role in how the package functions, after building the component with `yarn build` all of the distribution files will be inside the `dist` folder

```
"files": ["dist", "src/components/"],
```

Additionally, we will be adding the `src/components` folder because the components are exported from there.

- name your GIT repository inside the `repository` property

```
"repository": {
  "type": "git",
  "url": "git+https://github.com/MatijaNovosel/tri-state-checkbox.git"
},
```

- specify some keywords

```
"keywords": [
  "checkbox",
  "material",
  "material-ui",
  "tri-state-checkbox",
  "vue3",
  "vue",
  "vuejs"
],
```

- set the main entry point of the package through the `main` property

```
"main": "./dist/tri-state-checkbox.umd.cjs",
```

- set the `module` property, it is overshadowed by the `main` property as [noted here](https://stackoverflow.com/questions/42708484/what-is-the-module-package-json-field-for) but still required

```
"module": "./dist/tri-state-checkbox.js",
```

- define the `types` property for the package types generated by the `dts` package

```
"types": "./dist/index.d.ts",
```

- set the `exports` property which will be used for distributing the package files

```
"exports": {
  ".": {
    "import": "./dist/vue-material-time-picker.js",
    "require": "./dist/vue-material-time-picker.umd.cjs"
  },
  "./dist/style.css": {
    "import": "./dist/style.css",
    "require": "./dist/style.css"
  }
},
```

- finally, add the `peerDependencies` property which only contains `vue`, this part will be used to define which packages this package relies on

```
"peerDependencies": {
  "vue": "^3.0.0"
},
```

The full `package.json` file is as follows:

```json
{
  "name": "tri-state-checkbox",
  "private": false,
  "version": "0.0.1",
  "type": "module",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/MatijaNovosel/tri-state-checkbox.git"
  },
  "keywords": [
    "checkbox",
    "tri-state-checkbox",
    "material",
    "material-ui",
    "vue3",
    "vue",
    "vuejs"
  ],
  "files": ["dist", "src/components/"],
  "main": "./dist/tri-state-checkbox.umd.cjs",
  "module": "./dist/tri-state-checkbox.js",
  "exports": {
    ".": {
      "import": "./dist/tri-state-checkbox.js",
      "require": "./dist/tri-state-checkbox.umd.cjs"
    },
    "./dist/style.css": {
      "import": "./dist/style.css",
      "require": "./dist/style.css"
    }
  },
  "types": "./dist/index.d.ts",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "sass": "^1.62.1",
    "vue": "^3.2.47"
  },
  "peerDependencies": {
    "vue": "^3.0.0"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^4.1.0",
    "path": "^0.12.7",
    "typescript": "^5.0.2",
    "vite": "^4.3.9",
    "vite-plugin-dts": "^2.3.0",
    "vue-tsc": "^1.4.2"
  }
}
```

Don't forget to set the version to `0.0.1` or something of your choosing other than `0.0.0`.

## Distributing the package

First you will need to log in with `npm login`, complete the process then run `npm publish`.
