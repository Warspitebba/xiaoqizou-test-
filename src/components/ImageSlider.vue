<template>
  <div class="image-slider">
    <div class="slides" :style="{ transform: `translateX(-${currentIndex * 100}%)` }">
      <div
        v-for="(image, index) in images"
        :key="index"
        class="slide"
        :style="{
          backgroundImage: loadedImages[index] ? `url(${loadedImages[index]})` : 'none',
          opacity: index === currentIndex ? 1 : 0.3,
          transform: `rotateY(${index === currentIndex ? 0 : 45}deg) scale(${
            index === currentIndex ? 1 : 0.8
          })`
        }"
        v-lazy-load="{
          src: image,
          index: index,
          loadCallback: (imgSrc, idx) => handleImageLoad(imgSrc, idx)
        }"
      >
        <!-- 占位元素（低质量图片或纯色背景） -->
        <div v-if="!loadedImages[index]" class="placeholder"></div>
      </div>
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

// 图片数组（保持你的原始格式）
const images = [
  new URL('@/assets/image1.png', import.meta.url).href,
  new URL('@/assets/image2.jpg', import.meta.url).href,
  new URL('@/assets/image3.png', import.meta.url).href,
  new URL('@/assets/image4.jpg', import.meta.url).href,
];

const currentIndex = ref(0);
const loadedImages = ref([]); // 存储已加载的图片URL

// 图片加载完成回调
const handleImageLoad = (imgSrc, index) => {
  loadedImages.value[index] = imgSrc;
};

// 自定义懒加载指令
const vLazyLoad = {
  mounted(el, binding) {
    const { src, index, loadCallback } = binding.value;

    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          // 加载真实图片
          const img = new Image();
          img.src = src;
          img.onload = () => loadCallback(src, index);
          observer.unobserve(el); // 停止观察
        }
      });
    }, {
      rootMargin: '100px', // 提前100px触发加载
      threshold: 0.01
    });

    observer.observe(el);
  }
};

// 预加载当前和相邻的图片（提升用户体验）
const preloadAdjacentImages = () => {
  const indicesToLoad = [
    currentIndex.value,
    currentIndex.value + 1,
    currentIndex.value - 1
  ].filter(idx => idx >= 0 && idx < images.length);

  indicesToLoad.forEach(idx => {
    if (!loadedImages.value[idx]) {
      const img = new Image();
      img.src = images[idx];
      img.onload = () => handleImageLoad(images[idx], idx);
    }
  });
};

// 原有轮播方法（增加预加载）
const nextSlide = () => {
  currentIndex.value = (currentIndex.value + 1) % images.length;
  preloadAdjacentImages();
  startAutoPlay();
};

const prevSlide = () => {
  currentIndex.value = (currentIndex.value - 1 + images.length) % images.length;
  preloadAdjacentImages();
  startAutoPlay();
};
let timer= null; // 定时器 ID
// 启动自动轮播
const startAutoPlay = () => {
  if (timer) {
    clearInterval(timer);
    timer = null; // 清除现有的定时器
  }
  timer = setInterval(() => {
    nextSlide();
  }, 5000); // 每 5 秒切换一次
};
// 切换到指定图片
const goToSlide = (index) => {
  currentIndex.value = index;
  startAutoPlay(); // 重新启动自动轮播
};
onMounted(() => {
  // 初始预加载第一张和相邻图片
  preloadAdjacentImages();
});
</script>

<style scoped>
/* 新增占位符样式 */
.placeholder {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(45deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 200%;
  animation: placeholderShimmer 1.5s linear infinite;
}

@keyframes placeholderShimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
.image-slider {
  position: relative;
  width: 80vw; /* 宽度占视口的 80%，但是不溢出*/
  max-width: 100%;
  height: 80vh; /* 高度占视口的 80% */
  margin: 0 auto; /* 水平居中 */
  overflow: hidden; /* 隐藏溢出的内容 */
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center; /* 垂直居中 */
  border-radius: 20px;
  box-shadow: 0 40px 80px rgba(0, 0, 0, 0.8);
  background-color: aliceblue;
  border: 10px solid rgba(128, 0, 128, 0.4);
}

.slides {
  display: flex;
  width: 100%; /* 宽度占父容器80% */
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
  width: 60px;
  height: 60px;
  cursor: pointer;
  border-radius: 50%;
  font-size: 40px; /* 增大按钮图标尺寸 */
  transition: background 0.2s ease;
}
@media (max-width: 1080px){
  .image-slider {
    width: 80vw;/* 确保不溢出 */
    height: 40vh;
  }
  .nav-btn {
    width: 40px;
    height: 40px;
    font-size: 20px;
  }
  .dots span {
    width: 10px; /* 圆点尺寸 */
    height: 10px; /* 圆点尺寸 */
    background: rgba(255, 255, 255, 0.5);
    border-radius: 50%;
    cursor: pointer;
    transition: background 0.3s ease;
  }

  .dots span.active {
    width: 14px; /* 增大圆点尺寸 */
    height: 14px; /* 增大圆点尺寸 */
    background: rgba(255, 255, 255, 1);
  }
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
  width: 20px; /* 增大圆点尺寸 */
  height: 20px; /* 增大圆点尺寸 */
  background: rgba(255, 255, 255, 1);
}

.dots span:hover {
  background: rgba(255, 255, 255, 0.8);
}
</style>
