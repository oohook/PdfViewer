<template>
    <div class="ep-image-viewer__wrapper" v-show="dialogVisible">
        <div class="ep-image-viewer__mask"></div>
        <span class="ep-image-viewer__btn ep-image-viewer__close" @click="close">
            <el-icon>
                <Close />
            </el-icon>
        </span>
        <span class="ep-image-viewer__btn ep-image-viewer__prev" @click="prevPage">
            <el-icon>
                <ArrowLeft />
            </el-icon>
        </span>
        <span class="ep-image-viewer__btn ep-image-viewer__next" @click="nextPage">
            <el-icon>
                <ArrowRight />
            </el-icon>
        </span>
        <div class="ep-image-viewer__btn ep-image-viewer__progress">{{ state.currentPage }} / {{ state.pageCount }}
        </div>
        <div class="ep-image-viewer__btn ep-image-viewer__actions">
            <div class="ep-image-viewer__actions__inner">
                <i class="ep-icon" @click="zoomOut">
                    <el-icon>
                        <ZoomOut />
                    </el-icon>
                </i>
                <i class="ep-icon" @click="resetZoom">
                    <el-icon>
                        <FullScreen />
                    </el-icon>
                </i>
                <i class="ep-icon" @click="zoomIn">
                    <el-icon>
                        <ZoomIn />
                    </el-icon>
                </i>
                <i class="ep-icon" @click="rotateLeft">
                    <el-icon>
                        <RefreshLeft />
                    </el-icon>
                </i>
                <i class="ep-icon" @click="rotateRight">
                    <el-icon>
                        <RefreshRight />
                    </el-icon>
                </i>
            </div>
        </div>
        <div class="ep-image-viewer__canvas">
            <vue-pdf-embed :source="state.source" :style="pdfStyle" class="ep-image-viewer__img"
                @rendered="handlePdfRendered" @mousedown="startDrag" @mousemove="onDrag" @mouseup="stopDrag"
                @mouseleave="stopDrag" />
        </div>
    </div>
</template>

<script setup lang="ts">
import {
    ArrowLeft,
    ArrowRight,
    Close,
    FullScreen,
    RefreshLeft,
    RefreshRight,
    ZoomIn,
    ZoomOut
} from '@element-plus/icons-vue'
import { computed, reactive, ref } from 'vue'
import VuePdfEmbed from 'vue-pdf-embed'

const props = defineProps({
  pdfList: {
    type: Array as () => string[],
    required: true,
  },
})

const dialogVisible = ref(false)
const isLoaded = ref(false)
const isDragging = ref(false)
const rotation = ref(0)
const startPos = ref({ x: 0, y: 0 })
const position = ref({ x: 0, y: 0 })
const state = reactive({
    source: '' as string,
    pageCount: props.pdfList.length || 0,
    currentPage: 1,
    scale: 1,
})
const pdfStyle = computed(() => ({
    transform: `scale(${state.scale}) translate(${position.value.x}px, ${position.value.y}px) rotate(${rotation.value}deg)`,
    cursor: isDragging.value ? 'grabbing' : 'grab',
    opacity: isLoaded.value ? 1 : 0
}))

const show = async (page: number) => {
    resetZoom()
    state.currentPage = page
    isLoaded.value = false
    dialogVisible.value = true
    state.source = props.pdfList[page - 1]
}

defineExpose({
    show
})

const close = () => {
    resetZoom()
    state.source = ''
    dialogVisible.value = false
}
const prevPage = () => {
    if (state.currentPage > 1){
        show(state.currentPage - 1)
    }
}
const nextPage = () => {
    if (state.currentPage < state.pageCount){
        show(state.currentPage + 1)
    }
}
const zoomIn = () => {
  state.scale = Math.min(state.scale + 0.3, 3)
}
const zoomOut = () => {
    state.scale = Math.max(state.scale - 0.3, 0.5)
    position.value = { x: 0, y: 0 }
}
const resetZoom = () => {
    rotation.value=0
    state.scale = 1
    position.value = { x: 0, y: 0 }
}
const rotateLeft = () => {
    rotation.value -= 90
}
const rotateRight = () => {
    rotation.value += 90
}

const startDrag = (e: MouseEvent) => {
    isDragging.value = true
    startPos.value = {
        x: e.clientX - position.value.x,
        y: e.clientY - position.value.y
    }
}
const onDrag = (e: MouseEvent) => {
    if (!isDragging.value) return
    position.value = {
        x: e.clientX - startPos.value.x,
        y: e.clientY - startPos.value.y
    }
}
const stopDrag = () => {
    isDragging.value = false
}

const handlePdfRendered = () => {
    isLoaded.value = true 
}
</script>

<style scoped>
.ep-image-viewer__wrapper {
  z-index: 2005;
}
.ep-image-viewer__img {
    width: 35%;
}
.ep-image-viewer__canvas {
    transition: opacity 0.3s ease;
}
</style>