<script setup lang="ts">
import { defineProps } from 'vue'
import type { ShowCaseInfo } from '../../showcases'

defineProps<{
  info: ShowCaseInfo
}>()
</script>

<template>
  <div>
    <div
      class="block mb-1.5 w-full overflow-hidden relative aspect-9/16"
      border="~ rounded gray-400 opacity-20"
    >
      <img :src="info.cover" class="absolute top-0 bottom-0 left-0 right-0" />
    </div>
    <div class="font-bold">
      {{ info.title }}
    </div>
    <div
      class="text-current text-xs opacity-75 whitespace-nowrap overflow-hidden overflow-ellipsis"
    >
      {{ info.at }}
    </div>
    <div class="mt-2 flex">
      <a
        v-if="info.author.link"
        :href="info.author.link"
        class="text-current text-sm opacity-50"
        target="_blank"
      >{{ info.author.name }}</a>
      <div v-else class="text-current text-sm opacity-50">
        {{ info.author.name }}
      </div>
      <div class="flex-auto"></div>
      <a
        v-if="info.videoLink"
        :href="info.videoLink"
        class="ml-2 text-current opacity-20 hover:opacity-100 hover:text-[#cb3837]"
        target="_blank"
      >
        <carbon:video />
      </a>
      <a
        v-if="info.slidesLink"
        :href="info.slidesLink"
        class="ml-2 text-current opacity-20 hover:opacity-100 hover:text-[#cb3837]"
        target="_blank"
      >
        <carbon:presentation-file />
      </a>
      <a
        v-if="info.sourceLink"
        :href="info.sourceLink"
        class="ml-2 text-current opacity-20 hover:opacity-100"
        target="_blank"
      >
        <carbon:logo-github />
      </a>
    </div>
  </div>
</template>
