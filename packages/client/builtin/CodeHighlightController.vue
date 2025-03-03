<script setup lang="ts">
import { range, remove } from '@antfu/utils'
import { parseRangeString } from '@slidev/parser/core'
import { computed, defineProps, getCurrentInstance, inject, onMounted, onUnmounted, ref, watchEffect } from 'vue'
import { injectionClicks, injectionClicksElements, injectionClicksDisabled, CLASS_VCLICK_TARGET } from '../modules/directives'

const props = defineProps({
  ranges: {
    default: () => [],
  },
})

const clicks = inject(injectionClicks)!
const elements = inject(injectionClicksElements)!
const disabled = inject(injectionClicksDisabled)!

function makeid(length = 5) {
  const result = []
  const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
  const charactersLength = characters.length
  for (let i = 0; i < length; i++)
    result.push(characters.charAt(Math.floor(Math.random() * charactersLength)))
  return result.join('')
}

const el = ref<HTMLDivElement>()
const vm = getCurrentInstance()

onMounted(() => {
  const prev = elements.value.length
  const index = computed(() => {
    if (disabled.value)
      return props.ranges.length - 1
    return Math.min(Math.max(0, clicks.value - prev), props.ranges.length - 1)
  })
  const rangeStr = computed(() => props.ranges[index.value] || '')
  if (props.ranges.length >= 2 && !disabled.value) {
    const id = makeid()
    const ids = range(props.ranges.length - 1).map(i => id + i)
    elements.value.push(...ids)
    onUnmounted(() => ids.forEach(i => remove(elements.value, i)), vm)
  }

  watchEffect(() => {
    if (!el.value)
      return
    const isDuoTone = el.value.querySelector('.shiki-dark')
    const targets = isDuoTone ? Array.from(el.value.querySelectorAll('.shiki')) : [el.value]
    for (const target of targets) {
      const lines = Array.from(target.querySelectorAll('.line'))
      const highlights: number[] = parseRangeString(lines.length, rangeStr.value)
      lines.forEach((line, idx) => {
        const highlighted = highlights.includes(idx)
        line.classList.toggle(CLASS_VCLICK_TARGET, true)
        line.classList.toggle('highlighted', highlighted)
        line.classList.toggle('dishonored', !highlighted)
      })
    }
  })
})
</script>

<template>
  <div ref="el">
    <slot />
  </div>
</template>
