<script setup lang="ts">
import { nextTick, ref } from 'vue'
import type { Content } from './utils/type'

const title = ref<string>('')
const titleRef = ref<HTMLElement | null>(null)
const contents = ref<Content[]>([])
const contentRefs = ref<HTMLElement[]>([])

const handleTitleKeyDown = async (event: KeyboardEvent) => {
  if (event.key === 'Enter') {
    if (!event.isComposing) {
      contents.value.splice(0, 0, { id: 0, text: '' })
      contents.value.forEach((content, index) => {
        content.id = index
      })
      await nextTick()
      setTimeout(() => {
        contentRefs.value[0]?.focus()
      }, 10)
    }
  } else if (event.key === 'ArrowDown') {
    if (contentRefs.value) {
      contentRefs.value[0].focus()
    }
  }
}

const handleKeyDown = async (event: KeyboardEvent, index: number) => {
  if (event.key === 'Enter') {
    if (event.shiftKey) {
      return
    } else {
      event.preventDefault()

      if (!event.isComposing) {
        contents.value.splice(index + 1, 0, { id: 0, text: '' })
        contents.value.forEach((content, index) => {
          content.id = index
        })
        await nextTick()
        setTimeout(() => {
          contentRefs.value[index + 1]?.focus()
        }, 10)
      }
    }
  } else if (event.key === 'ArrowDown') {
    if (index < contents.value.length - 1) {
      contentRefs.value[index + 1]?.focus()
    }
  } else if (event.key === 'ArrowUp') {
    if (index === 0) {
      titleRef.value?.focus()
    } else {
      contentRefs.value[index - 1]?.focus()
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
      <h1
        contenteditable="true"
        @keydown="(e) => handleTitleKeyDown(e)"
        @input="(e) => (title = (e.target as HTMLElement).innerText)"
        ref="titleRef"
        class="w-full border mb-2 text-6xl"
      >
        {{ title }}
      </h1>
      <p
        v-for="(content, index) in contents"
        v-bind:key="content.id"
        contenteditable="true"
        @keydown="(e) => handleKeyDown(e, index)"
        @input="(e) => (content.text = (e.target as HTMLElement).innerText)"
        v-bind:ref="
          (el) => {
            if (el) contentRefs[index] = el as HTMLElement
          }
        "
        class="w-full mb-2 border"
      >
        {{ content.text }}
      </p>
    </div>
  </div>
</template>
