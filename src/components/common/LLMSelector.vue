<script setup lang="ts">
import { h } from 'vue'
import { NButton, NDropdown, NTooltip } from 'naive-ui'
import type { VNode } from 'vue'
import type { DropdownGroupOption, DropdownOption } from 'naive-ui'
import AvatarComponent from '@/views/chat/components/Message/Avatar.vue'
import { useAppStore } from '@/store'

const appStore = useAppStore()

function renderOption({ node, option }: { node: VNode; option: DropdownOption | DropdownGroupOption }) {
  if (option.enable && option.isFree) {
    return h(NTooltip, { placement: 'left' }, {
      trigger: () => node,
      default: () => 'Token额度无限',
    })
  } else {
    return node
  }
}
function renderLabel(option: DropdownOption) {
  const val = option.value as string
  const modelPlatform = appStore.getLLMById(val)?.modelPlatform
  const isSelected = option.modelId === appStore.selectedLLM.modelId
  return [
    h('div', { class: 'flex items-center' }, {
      default: () => [
        h(
          'span',
          {
            class: option.isFree ? 'text-[var(--n-option-text-color-active)]' : 'text-orange-500',
          },
          '⨀ ',
        ),
        h(
          AvatarComponent,
          {
            name: modelPlatform,
            imageSize: 20,
          },
        ),
        h(
          'div',
          {
            class: isSelected ? 'ml-1.5 text-[var(--n-option-text-color-active)]' : 'ml-1.5',
          },
          { default: () => option.label as string },
        ),
        isSelected && h(
          'i',
          { class: 'n-base-icon text-[var(--n-option-text-color-active)] ml-auto' },
          h(
            'svg',
            {
              xmlns: 'http://www.w3.org/2000/svg',
              viewBox: '0 0 16 16',
            },
            h('g', { fill: 'none' }, [
              h('path', {
                d: 'M14.046 3.486a.75.75 0 0 1-.032 1.06l-7.93 7.474a.85.85 0 0 1-1.188-.022l-2.68-2.72a.75.75 0 1 1 1.068-1.053l2.234 2.267l7.468-7.038a.75.75 0 0 1 1.06.032z',
                fill: 'currentColor',
              }),
            ]),
          ),
        ),
      ],
    }),
  ]
}

function handleSelect(key: string | number) {
  appStore.setSelectedLLM(`${key}`)
  console.log('=>(LLMSelector.vue:12) ', appStore.selectedLLM)
}
</script>

<template>
  <NDropdown
    size="small" placement="top-start" trigger="click" :show-arrow="true" :render-label="renderLabel"
    :render-option="renderOption" :options="appStore.llms" @select="handleSelect"
  >
    <NButton icon-placement="right" class="max-w-full">
      <AvatarComponent :name="appStore.selectedLLM.modelPlatform" :image-size="20" class="mr-1 shrink-0" />
      <span class="truncate">
        {{ appStore.selectedLLM.modelTitle || appStore.selectedLLM.modelName }}
      </span>
    </NButton>
  </NDropdown>
</template>
