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
import { ref } from "vue";

withDefaults(
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

const val = ref<boolean | null>(false);

const change = () => {
  if (val.value === false) val.value = null;
  else if (val.value === null) val.value = true;
  else val.value = false;
};
</script>

<style lang="scss">
.m-checkbox {
  z-index: 0;
  position: relative;
  display: inline-block;
  font-size: 16px;
  line-height: 1.5;
  > input {
    appearance: none;
    -moz-appearance: none;
    -webkit-appearance: none;
    z-index: -1;
    position: absolute;
    left: -10px;
    top: -8px;
    display: block;
    margin: 0;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    box-shadow: none;
    outline: none;
    opacity: 0;
    transform: scale(1);
    pointer-events: none;
    transition: opacity 0.3s, transform 0.2s;
    &:checked {
      background-color: v-bind(color);
      + {
        span {
          &::before {
            border-color: v-bind(color);
            background-color: v-bind(color);
          }
          &::after {
            border-color: white;
          }
        }
      }
      &:active {
        + {
          span {
            &::before {
              border-color: transparent;
              background-color: grey;
            }
          }
        }
      }
      &:disabled {
        + {
          span {
            &::before {
              border-color: transparent;
              background-color: currentColor;
            }
          }
        }
      }
    }
    &:indeterminate {
      background-color: v-bind(color);
      + {
        span {
          &::before {
            border-color: v-bind(color);
            background-color: v-bind(color);
          }
          &::after {
            border-color: white;
            border-left: none;
            transform: translate(4px, 3px);
          }
        }
      }
      &:disabled {
        + {
          span {
            &::before {
              border-color: transparent;
              background-color: currentColor;
            }
          }
        }
      }
    }
    &:focus {
      opacity: 0.12;
    }
    &:active {
      opacity: 1;
      transform: scale(0);
      transition: transform 0s, opacity 0s;
      + {
        span {
          &::before {
            border-color: v-bind(color);
          }
        }
      }
    }
    &:disabled {
      opacity: 0;
      + {
        span {
          color: grey;
          cursor: initial;
          &::before {
            border-color: currentColor;
          }
        }
      }
    }
  }
  > span {
    display: inline-block;
    width: 100%;
    cursor: pointer;
    &::before {
      content: "";
      display: inline-block;
      box-sizing: border-box;
      margin: 3px 11px 3px 1px;
      border: solid 2px;
      border-color: grey;
      border-radius: 2px;
      width: 18px;
      height: 18px;
      vertical-align: top;
      transition: border-color 0.2s, background-color 0.2s;
    }
    &::after {
      content: "";
      display: block;
      position: absolute;
      top: 3px;
      left: 1px;
      width: 10px;
      height: 5px;
      border: solid 2px transparent;
      border-right: none;
      border-top: none;
      transform: translate(3px, 4px) rotate(-45deg);
    }
  }
  &:hover {
    > input {
      opacity: 0.04;
      &:focus {
        opacity: 0.16;
      }
    }
  }
}
</style>
