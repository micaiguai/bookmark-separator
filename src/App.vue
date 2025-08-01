<script lang="ts" setup>
import { useElementSize } from '@vueuse/core'
import { computed, ref, useTemplateRef, watch } from 'vue'

const BOOKMARK_SYMBOL_LENGTH = 11
const SYMBOL = '─'
const DEFAULT_BOOKMARK = SYMBOL.repeat(BOOKMARK_SYMBOL_LENGTH)

const uri = ref('')
const input = ref('')
const bookmark = ref('')
const defaultSizeContainer = useTemplateRef('defaultSizeContainer')
const dynamicSizeContainer = useTemplateRef('dynamicSizeContainer')
const { width: defaultWidth } = useElementSize(defaultSizeContainer)
const { width: dynamicWidth } = useElementSize(dynamicSizeContainer)

function dragStart() {
  refreshUri()
  location.href = uri.value
}

const unitWidth = computed(() => {
  return defaultWidth.value / BOOKMARK_SYMBOL_LENGTH
})

function refreshUri() {
  uri.value = `/#/?timestamp=${Math.random()}`
}

watch(input, () => {
  if (input.value === '') {
    bookmark.value = DEFAULT_BOOKMARK
    return
  }
  bookmark.value = input.value
}, {
  immediate: true,
})

watch(dynamicWidth, () => {
  const widthGap = defaultWidth.value - dynamicWidth.value
  if (widthGap < 0)
    return
  const symbolCount = Math.ceil(widthGap / unitWidth.value)
  const half = Math.floor(symbolCount / 2)
  bookmark.value = `${SYMBOL.repeat(half)}${bookmark.value}${SYMBOL.repeat(symbolCount - half)}`
})
</script>

<template>
  <div class="bg-gray-100 flex flex-col items-center justify-center h-dvh">
    <input v-model="input" class="mb-4 p-2 rounded ring ring-gray/30" placeholder="Custom bookmark" type="text">
    <div class="mb-2 p-2 rounded-full bg-blue-600 inline-flex min-h-20 w-48 cursor-pointer select-none items-center justify-center relative">
      <div class="text-3xl text-white cursor-pointer pointer-events-none left-50% top-50% absolute -translate-50%">
        Drag It
      </div>
      <a :href="uri" class="text-transparent size-full cursor-pointer" @mousedown="dragStart">
        {{ bookmark }}
      </a>
    </div>
    <div class="text-3xl mb-4">
      To your bookmarks toolbar
    </div>
    <div class="w-content p-2 rounded min-h-12 ring ring-gray/30">
      <div class="text-gray/60">
        Preview
      </div>
      {{ bookmark }}
    </div>
    <div ref="defaultSizeContainer" class="">
      {{ DEFAULT_BOOKMARK }}
    </div>
    <div ref="dynamicSizeContainer" class="">
      {{ bookmark }}
    </div>
  </div>
</template>
