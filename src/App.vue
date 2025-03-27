<template>
  <div id="app">
    <div class="header">
      <MusicPlay @toggle-background="handleBackgroundChange" />
      <h1>宵崎奏图片展示</h1>
      <CheckOut v-model="isMusicPlaying"/>
    </div>
    <ImageSlider />
  </div>
</template>

<script setup lang="ts">
import ImageSlider from '@/components/ImageSlider.vue';
import MusicPlay from '@/components/MusicPlay.vue';
import CheckOut from './components/CheckOut.vue';
import { ref } from 'vue';

const isMusicPlaying = ref(false)
const handleBackgroundChange = (isPlaying: boolean) => {
  console.log('handleBackgroundChange 被调用，isPlaying:', isPlaying); // 调试用
  const body = document.body;
  isMusicPlaying.value = isPlaying;
  if (isPlaying) {
    body.classList.add('background-active'); // 添加类，显示背景图片
    console.log('背景图片已设置'); // 调试用
  } else {
    body.classList.remove('background-active'); // 移除类，隐藏背景图片
    console.log('背景图片已移除'); // 调试用
  }
};
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 90vh;
  box-sizing: border-box;
}
.header {
  display: flex; /* 始终垂直排列（PC端和移动端） */
  align-items: center;
  justify-content: center;
  gap: 20px;  /* 标题和下方控件的间距 */
  width: 100%;
  margin-bottom: 20px;
}
.controls {
  display: flex;
  gap: 20px;  /* MusicPlay 和 CheckOut 之间的间距 */
  align-items: center;
}
h1 {
  text-align: center;
  background-color: rgb(175, 185, 255);
  color: black;
  border-radius: 5px;
  padding: 10px 10px;
  margin: 0;
}
/* 移动端适配 */
@media (max-width: 1080px) {
  .header {
    gap: 8px;  /* 减小间距 */
  }
  h1 {
    font-size: 20px;  /* 减小字体大小 */
    padding: 4px 4px;
  }
  .controls {
    gap: 12px;
  }
}
/* 极小屏幕（如iPhone SE）优化 */
@media (max-width: 375px) {
  h1 {
    font-size: 10px;
  }
}
</style>
<style>
/* 全局 body 样式保持不变 */
body {
  font-family: Arial, sans-serif;
  margin: 0;
  background-color: #bb6688;
}
body::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url('@/assets/bg1.jpg');
  background-size: cover;
  background-position: center;
  opacity: 0;
  transition: opacity 1.5s ease;
  z-index: -1;
}
body.background-active::before {
  opacity: 1;
}
</style>
