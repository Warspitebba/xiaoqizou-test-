<template>
  <div id="app">
    <div class="header">
      <h1>烧鸡宵崎奏</h1>
      <MusicPlay @toggle-background="handleBackgroundChange" />
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
  align-items: center; /* 水平居中 */
  min-height: 90vh; /* 高度 */
  box-sizing: border-box; /* 包含内边距在宽度和高度内 */
}
.header {
  display: flex; /* 将 h1 和 Music 组件放在同一行 */
  align-items: center; /* 垂直居中 */
  justify-content: center; /* 水平居中 */
  gap: 20px; /* h1 和按钮之间的间距 */
  width: 100%;
  margin-bottom: 20px; /* 与下方内容的间距 */
}
h1 {
  background-color: rgb(175, 185, 255);
  color: black;
  border-radius: 5px;
}
body {
  font-family: Arial, sans-serif;
  margin: 0;
  background-color: #bb6688;
  background-size: cover; /* 确保背景图片覆盖整个页面 */
  background-position: center; /* 背景图片居中 */
  transition: background-image 1s ease; /* 添加过渡效果 */
}
body::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url('@/assets/bg1.jpg'); /* 背景图片路径 */
  background-size: cover;
  background-position: center;
  opacity: 0; /* 初始透明度为 0 */
  transition: opacity 1.5s ease; /* 淡入淡出过渡效果，持续 1.5 秒 */
  z-index: -1; /* 放在页面内容下方 */
}
/* 添加背景图片激活样式 */
body.background-active::before {
  opacity: 1; /* 完全显示 */
}
</style>
