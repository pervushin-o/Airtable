<script setup lang="ts">
import type { VNodeRef } from '@vue/runtime-core'
import { EditModeInj, ReadonlyInj, inject, ref, useVModel } from '#imports'

interface Props {
  modelValue?: string | null
}

const props = defineProps<Props>()

const emits = defineEmits(['update:modelValue'])

const { showNull } = useGlobal()

const editEnabled = inject(EditModeInj)

const rowHeight = inject(RowHeightInj)

const readonly = inject(ReadonlyInj, ref(false))

const vModel = useVModel(props, 'modelValue', emits)

const focus: VNodeRef = (el) => {
  ;(el as HTMLInputElement)?.focus()
}
</script>

<template>
  <input
    v-if="!readonly && editEnabled"
    :ref="focus"
    v-model="vModel"
    class="h-full w-full outline-none bg-transparent"
    :class="{ '!px-2': editEnabled }"
    @blur="editEnabled = false"
    @keydown.down.stop
    @keydown.left.stop
    @keydown.right.stop
    @keydown.up.stop
    @keydown.delete.stop
    @selectstart.capture.stop
    @mousedown.stop
  />

  <span v-else-if="vModel === null && showNull" class="nc-null">NULL</span>

  <LazyCellClampedText v-else :value="vModel" :lines="rowHeight" />
</template>
