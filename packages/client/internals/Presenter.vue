<script setup lang="ts">
import { useHead } from '@vueuse/head'
import { ref, computed } from 'vue'
import { total, currentPage, currentRoute, nextRoute, clicks, clicksElements, useSwipeControls, clicksTotal, hasNext } from '../logic/nav'
import { showOverview } from '../state'
import { configs } from '../env'
import { registerShotcuts } from '../logic/shortcuts'
import SlideContainer from './SlideContainer.vue'
import NavControls from './NavControls.vue'
import SlidesOverview from './SlidesOverview.vue'
import NoteEditor from './NoteEditor.vue'
import Goto from './Goto.vue'

registerShotcuts()

useHead({
  title: configs.title ? `Presenter - ${configs.title} - Slidev` : 'Presenter - Slidev',
})

const main = ref<HTMLDivElement>()
const nextTabElements = ref([])
const nextSlide = computed(() => {
  if (clicks.value < clicksTotal.value) {
    return {
      route: currentRoute.value,
      clicks: clicks.value + 1,
    }
  }
  else {
    if (hasNext.value) {
      return {
        route: nextRoute.value,
        clicks: 0,
      }
    }
    else {
      return null
    }
  }
})

useSwipeControls(main)
</script>

<template>
  <div class="bg-main h-full">
    <div class="grid-container">
      <div class="grid-section top flex">
        <img src="../assets/logo-title-horizontal.png" class="h-14 ml-2 py-2 my-auto" />
        <div class="flex-auto" />
      </div>
      <div ref="main" class="grid-section main flex flex-col p-4">
        <SlideContainer
          key="main"
          v-model:clicks="clicks"
          v-model:clicks-elements="clicksElements"
          class="h-full w-full"
          :route="currentRoute"
          :clicks-disabled="false"
        />
      </div>
      <div class="grid-section next flex flex-col p-4">
        <SlideContainer
          v-if="nextSlide"
          key="next"
          v-model:clicks-elements="nextTabElements"
          class="h-full w-full"
          :clicks="nextSlide.clicks"
          :route="nextSlide.route"
          :clicks-disabled="false"
        />
      </div>
      <div class="grid-section note">
        <NoteEditor class="w-full h-full p-4 overflow-auto" />
      </div>
      <div class="grid-section bottom">
        <NavControls />
      </div>
    </div>
    <div class="progress-bar">
      <div class="progress h-2px bg-primary transition-all" :style="{width: `${(currentPage - 1) / (total - 1) * 100}%`}"></div>
    </div>
  </div>
  <Goto />
  <SlidesOverview v-model="showOverview" />
</template>

<style lang="postcss" scoped>
.section-title {
  @apply px-4 py-2 font-xl;
}

.grid-container {
  @apply h-full w-full bg-gray-400 bg-opacity-15;
  display: grid;
  gap: 1px 1px;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: min-content 2fr 1fr min-content;
  grid-template-areas:
    "top top"
    "main main"
    "note next"
    "bottom bottom";
}

@screen md {
  .grid-container {
    grid-template-columns: 1fr 1.1fr 0.9fr;
    grid-template-rows: min-content 1fr 2fr min-content;
    grid-template-areas:
      "top top top"
      "main main next"
      "main main note"
      "bottom bottom bottom";
  }
}

.progress-bar {
  @apply fixed left-0 right-0 bottom-0;
}

.grid-section {
  @apply bg-main;

  &.top {
    grid-area: top;
  }
  &.main {
    grid-area: main;
  }
  &.next {
    grid-area: next;
  }
  &.note {
    grid-area: note;
  }
  &.bottom {
    grid-area: bottom;
  }
}
</style>
