<template>
  <div class="music-container">
    <button class="music-button" @click="toggleMusic">
      {{ isPlaying ? '播放音乐' : '播放音乐' }}
    </button>
  </div>
</template>

<script setup name =" MusicPlay">
import { ref } from 'vue';

// 音乐文件路径
const musicFile = new URL('@/assets/music.mp3', import.meta.url).href;

const isPlaying = ref(false); // 是否正在播放音乐
let audio = null; // 音频对象

const emit = defineEmits(['toggle-background']); // 定义事件

// 播放或暂停音乐
const toggleMusic = () => {
  if (!audio) {
    audio = new Audio(musicFile); // 初始化音频
    audio.loop = false; // 设置循环播放
  }
  if (isPlaying.value) {
    audio.pause(); // 暂停音乐
    audio.currentTime = 0; // 重置播放位置
  } else {
    audio.play(); // 播放音乐
  }
  isPlaying.value = !isPlaying.value; // 切换播放状态
  emit('toggle-background', isPlaying.value); // 通知父组件状态变化
  console.log(1);
};
</script>

<style scoped>
.music-container {
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center; /* 垂直居中 */
}

.music-button {
  padding: 10px 20px;
  font-size: 16px;
  color: white;
  background-color: rgba(0, 0, 0, 0.7);
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.5s ease;
}
@media (max-width: 1080px) {
    .music-button {
    padding: 5px 10px;
    font-size: 16px;
    color: white;
    background-color: rgba(0, 0, 0, 0.7);
    border: none;
    border-radius: 3px;
    cursor: pointer;
    transition: background-color 0.5s ease;
  }
}
.music-button:hover {
  background-color: rgba(0, 0, 0, 0.9);
}
</style>

