<template>
  <div class="shape-element-operate">
    <BorderLine 
      class="operate-border-line"
      v-for="line in borderLines" 
      :key="line.type" 
      :type="line.type" 
      :style="line.style"
    />
    <template v-if="handlerVisible">
      <ResizeHandler
        class="operate-resize-handler" 
        v-for="point in resizeHandlers"
        :key="point.direction"
        :type="point.direction"
        :rotate="elementInfo.rotate"
        :style="point.style"
        @mousedown.stop="$event => scaleElement($event, elementInfo, point.direction)"
      />
      <RotateHandler
        class="operate-rotate-handler" 
        :style="{ left: scaleWidth / 2 + 'px' }"
        @mousedown.stop="rotateElement(elementInfo)"
      />
    </template>
  </div>
</template>

<script lang="ts">
export default {
  inheritAttrs: false,
}
</script>

<script lang="ts" setup>
import { computed, PropType } from 'vue'
import { storeToRefs } from 'pinia'
import { useMainStore } from '@/store'
import { PPTShapeElement } from '@/types/slides'
import { OperateResizeHandlers } from '@/types/edit'
import useCommonOperate from '../hooks/useCommonOperate'

import RotateHandler from './RotateHandler.vue'
import ResizeHandler from './ResizeHandler.vue'
import BorderLine from './BorderLine.vue'

const props = defineProps({
  elementInfo: {
    type: Object as PropType<PPTShapeElement>,
    required: true,
  },
  handlerVisible: {
    type: Boolean,
    required: true,
  },
  rotateElement: {
    type: Function as PropType<(element: PPTShapeElement) => void>,
    required: true,
  },
  scaleElement: {
    type: Function as PropType<(e: MouseEvent, element: PPTShapeElement, command: OperateResizeHandlers) => void>,
    required: true,
  },
})

const { canvasScale } = storeToRefs(useMainStore())

const scaleWidth = computed(() => props.elementInfo.width * canvasScale.value)
const scaleHeight = computed(() => props.elementInfo.height * canvasScale.value)
const { resizeHandlers, borderLines } = useCommonOperate(scaleWidth, scaleHeight)
</script>