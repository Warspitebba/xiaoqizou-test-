<template>
  <div class="checkbox-container" @click="toggleChecked">
    <input
      type="checkbox"
      :checked="modelValue"
      class="hidden-checkbox"
      readonly
    />
    <div class="progress-bar" :class="{ active: modelValue }">
      <div class="progress" :style="{ width: progressWidth }"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  modelValue: Boolean
})

const emit = defineEmits(['update:modelValue'])

const progressWidth = ref('0%') // 进度条宽度

// 切换状态
const toggleChecked = () => {
  emit('update:modelValue', !props.modelValue)
}

// 监听 modelValue 变化，更新进度条
watch(() => props.modelValue, (newVal) => {
  if (newVal) {
    progressWidth.value = '100%' // 勾选时进度条满
  } else {
    progressWidth.value = '0%' // 取消勾选时进度条归零
  }
}, { immediate: true })
</script>

<style scoped>
.hidden-checkbox {
  display: none;
}

.checkbox-container {
  display: inline-block;
  cursor: pointer;
}

.progress-bar {
  width: 100px; /* 进度条宽度 */
  height: 10px; /* 进度条高度 */
  background-color: #e0e0e0; /* 未填充时的背景色 */
  border-radius: 5px; /* 圆角 */
  overflow: hidden; /* 隐藏内部进度溢出部分 */
  position: relative;
}

.progress {
  height: 100%;
  background-color: pink; /* 进度条填充色 */
  transition: width 0.8s ease-in-out; /* 平滑过渡效果 */
}

.progress-bar.active .progress {
  width: 100%; /* 勾选时进度条满 */
}
</style>
