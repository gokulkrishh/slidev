<script setup lang="ts">
import { onClickOutside, useVModel } from '@vueuse/core'
import { defineEmit, defineProps, ref } from 'vue'

const emit = defineEmit()
const props = defineProps({
  modelValue: {
    default: false,
  },
  disabled: {
    default: false,
  },
})

const value = useVModel(props, 'modelValue', emit, { passive: true })
const el = ref<HTMLDivElement>()

onClickOutside(el, () => {
  value.value = false
})
</script>

<template>
  <div ref="el" class="flex relative">
    <div @click="value = !value" :class="{ disabled }">
      <slot name="button" :class="{ disabled }" />
    </div>
    <div
      v-show="value"
      class="rounded-md bg-main shadow absolute bottom-10 left-0 z-20"
      dark:border="~ gray-400 opacity-10"
    >
      <slot name="menu" />
    </div>
  </div>
</template>
