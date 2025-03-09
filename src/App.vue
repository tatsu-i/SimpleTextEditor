<script setup lang="ts">
import { nextTick, ref } from 'vue'
import type { Content } from './utils/type'

const contents = ref<Content[]>([{ id: 0, text: '' }])
const contentRefs = ref<HTMLElement[]>([])

const handleKeyDown = async (event: KeyboardEvent, index: number) => {
  if (event.key === 'Enter' && !event.shiftKey) {
    event.preventDefault()

    if (!event.isComposing) {
      contents.value.splice(index + 1, 0, { id: index + 1, text: '' })
      await nextTick()
      contentRefs.value[index + 1]?.focus()
    }
  }
}
</script>

<template>
  <div class="flex justify-center">
    <h1 class="text-4xl">Simple Text Editor</h1>
  </div>
  <div class="flex justify-center m-3">
    <div class="w-1/2">
      <p
        v-for="(content, index) in contents"
        :key="index"
        contenteditable="true"
        @keydown="(e) => handleKeyDown(e, index)"
        @input="(e) => (content.text = (e.target as HTMLElement).innerText)"
        v-bind:ref="
          (el) => {
            if (el) contentRefs[index] = el as HTMLElement
          }
        "
        placeholder="テキストを入力..."
        class="w-full rounded mb-2 focus:outline-none"
      >
        {{ content.text }}
      </p>
    </div>
  </div>
</template>
