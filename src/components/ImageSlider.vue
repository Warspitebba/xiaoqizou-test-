<template>
  <div class="image-slider">
    <!-- 幻灯片容器 -->
    <div class="slides" :style="{ transform: `translateX(-${currentIndex * 100}%)` }">
      <!-- 遍历图片列表，渲染每张图片 -->
      <div
        class="slide"
        v-for="(image, index) in images"
        :key="index"
        :style="{
          backgroundImage: `url(${image})`,
          opacity: index === currentIndex ? 1 : 0.3, // 当前图片不透明，其他图片半透明
          transform: `rotateY(${index === currentIndex ? 0 : 90}deg) scale(${
            index === currentIndex ? 1 : 0.8
          })`, // 旋转和缩放
        }"
      ></div>
    </div>
    <!-- 导航按钮 -->
    <button class="nav-btn prev" @click="prevSlide">&#10094;</button>
    <button class="nav-btn next" @click="nextSlide">&#10095;</button>
    <!-- 小圆点导航 -->
    <div class="dots">
      <span
        v-for="(dot, index) in images"
        :key="index"
        :class="{ active: index === currentIndex }"
        @click="goToSlide(index)"
      ></span>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// 图片路径列表
const images = [
  new URL('@/assets/image1.jpg', import.meta.url).href,
  new URL('@/assets/image2.jpg', import.meta.url).href,
  new URL('@/assets/image3.jpg', import.meta.url).href,
  new URL('@/assets/image4.jpg', import.meta.url).href,
];

// 当前显示的图片索引
const currentIndex = ref(0);

// 切换到上一张图片
const prevSlide = () => {
  currentIndex.value =
    currentIndex.value === 0 ? images.length - 1 : currentIndex.value - 1;
};

// 切换到下一张图片
const nextSlide = () => {
  currentIndex.value =
    currentIndex.value === images.length - 1 ? 0 : currentIndex.value + 1;
};

// 切换到指定图片
const goToSlide = (index) => {
  currentIndex.value = index;
};

// 自动轮播功能
onMounted(() => {
  setInterval(() => {
    nextSlide();
  }, 5000); // 每 5 秒切换一次
});
</script>

<style scoped>
.image-slider {
  position: relative;
  width: 80vw; /* 宽度占视口的 80% */
  height: 80vh; /* 高度占视口的 80% */
  margin: 0 auto; /* 水平居中 */
  overflow: hidden; /* 隐藏溢出的内容 */
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center; /* 垂直居中 */
  border-radius: 20px;
  box-shadow: 0 40px 80px rgba(0, 0, 0, 0.2);
  background-color: aliceblue;
}

.slides {
  display: flex;
  width: 80%; /* 宽度占父容器80% */
  height: 100%; /* 高度占满父容器 */
  transition: transform 0.5s ease-in-out;
}

.slide {
  flex: 0 0 100%; /* 每张幻灯片宽度为 100% */
  height: 100%;
  background-size: contain; /* 图片完整显示 */
  background-position: center; /* 图片居中 */
  background-repeat: no-repeat; /* 防止重复 */
  transition: transform 0.5s ease-in-out, opacity 0.5s ease-in-out; /* 添加过渡效果 */
  transform-origin: center; /* 旋转中心点 */
}

.nav-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(0, 0, 0, 0.5);
  color: white;
  border: none;
  padding: 20px; /* 增大按钮尺寸 */
  cursor: pointer;
  border-radius: 50%;
  font-size: 50px; /* 增大按钮图标尺寸 */
  transition: background 0.3s ease;
}

.nav-btn:hover {
  background: rgba(0, 0, 0, 0.8);
}

.prev {
  left: 10px; /* 左边距 */
}

.next {
  right: 10px; /* 右边距 */
}

.dots {
  position: absolute;
  bottom: 20px; /* 底部间距 */
  left: 50%;
  transform: translateX(-50%); /* 水平居中 */
  display: flex;
  gap: 10px; /* 圆点间距 */
}

.dots span {
  width: 15px; /* 圆点尺寸 */
  height: 15px; /* 圆点尺寸 */
  background: rgba(255, 255, 255, 0.5);
  border-radius: 50%;
  cursor: pointer;
  transition: background 0.3s ease;
}

.dots span.active {
  background: rgba(255, 255, 255, 1);
}

.dots span:hover {
  background: rgba(255, 255, 255, 0.8);
}
</style>
