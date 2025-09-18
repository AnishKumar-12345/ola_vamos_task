<template>
    <v-app>
      <v-sheet
        class="fill-height"
        elevation="0"
        :style="{
          backgroundImage: `url(${backgroundImage})`,
          backgroundSize: 'cover',
          backgroundPosition: 'center',
          backgroundRepeat: 'no-repeat',
        }"
      >
      <div
  class="content-wrapper"
  ref="scrollContainer"
  @scroll="onUserScroll"
>
          <div
            v-for="(slide, i) in slides"
            :key="i"
            class="content-card"
          >
            <div class="header">
              <img :src="logoImg" class="logo" alt="logo" />
              <p class="headline">{{ slide.title }}</p>
            </div>
            <p class="sub">{{ slide.subtitle }}</p>
            <img :src="slide.image" class="content-image" alt="content" />
          </div>
        </div>  
     
        <div class="dots-navigation">
          <div class="dots">
            <div
              v-for="(s, i) in slides"
              :key="i"
              :class="['dot', { active: i === currentIndex }]"
              @click="scrollToSlide(i)"
            ></div>
          </div>
        </div>
  
       
        <div class="sticky-btn-container">
          <v-btn class="download-btn"  dark>立即下载</v-btn>
        </div>
      </v-sheet>
    </v-app>
  </template>
  
  
  <script setup>
  import { ref, onMounted, onBeforeUnmount } from 'vue';
  import backgroundImage from '../assets/background.png';
  import logoImg from '../assets/logo.png';
  import screen1 from '../assets/screen 1.png';
  import screen2 from '../assets/screen 2.png';
  import screen3 from '../assets/screen 3.png';
  import screen4 from '../assets/screen 4.png';
  import screen5 from '../assets/screen 5.png';
  import screen6 from '../assets/screen 6.png';
  import screen7 from '../assets/screen 7.png';

  
  const currentIndex = ref(0);
  const scrollContainer = ref(null);
  
  const slides = [
    { image: screen1, title: '小鱼扑克 真人视频竞技', subtitle: '创新玩法 乐趣翻倍' },
    { image: screen2, title: '小鱼扑克 真人视频竞技', subtitle: '安全卫士巡航 护卫你的牌桌' },
    { image: screen3, title: '小鱼扑克 真人视频竞技', subtitle: '多种对战玩法实时竞技' },
    { image: screen4, title: '小鱼扑克 真人视频竞技', subtitle: '智能匹配系统 快速开局' },
    { image: screen5, title: '小鱼扑克 真人视频竞技', subtitle: '公平公正 绿色安全' },
    { image: screen6, title: '小鱼扑克 真人视频竞技', subtitle: '好友组局 私密安全' },
    { image: screen7, title: '小鱼扑克 真人视频竞技', subtitle: '好友组局 私密安全' },
  ];


let autoScrollInterval = null;

function handleScroll() {
  const container = scrollContainer.value;
  if (!container) return;

  const scrollTop = container.scrollTop;
  const containerHeight = container.clientHeight;

  let activeIndex = 0;
  let minDiff = Infinity;

  slides.forEach((_, i) => {
    const slide = container.children[i];
    const slideTop = slide.offsetTop;
    const slideCenter = slideTop - scrollTop - containerHeight / 2;

    const diff = Math.abs(slideCenter);
    if (diff < minDiff) {
      minDiff = diff;
      activeIndex = i;
    }
  });

  currentIndex.value = activeIndex;
}

function scrollToSlide(index) {
  const container = scrollContainer.value;
  const slide = container.children[index];
  if (slide) {
    container.scrollTo({
      top: slide.offsetTop,
      behavior: 'smooth',
    });
    currentIndex.value = index; 
    setTimeout(() => handleScroll(), 600);
  }
}



function startAutoScroll() {
  stopAutoScroll(); 
  autoScrollInterval = setInterval(() => {
    let nextIndex = currentIndex.value + 1;
    if (nextIndex >= slides.length) {
      nextIndex = 0;
    }
    scrollToSlide(nextIndex);
  }, 3000); 
}

function stopAutoScroll() {
  if (autoScrollInterval) {
    clearInterval(autoScrollInterval);
    autoScrollInterval = null;
  }
}

onMounted(() => {
  startAutoScroll();
});

onBeforeUnmount(() => {
  stopAutoScroll();
});

function resetAutoScroll() {
  stopAutoScroll();
  startAutoScroll();
}

function onUserScroll() {
  handleScroll();
  stopAutoScroll();

  clearTimeout(scrollTimeout);
  scrollTimeout = setTimeout(() => {
    startAutoScroll(); 
  }, 3000);
}
  </script>
  
  <style scoped>
  .fill-height {
    height: 100vh;
    width: 100vw;
    position: relative;
    overflow: hidden;
  }
  
  .content-wrapper {
    height: 100vh; 
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
  }
  
  .content-card {
    min-height: 100vh; 
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 24px;
    box-sizing: border-box;
  }
  
  .logo {
    width: 200px;
    margin-bottom: 10px;
  }
  
  .headline {
    font-size: 20px;
    font-weight: bold;
    color: white;
    margin-top: 10px;
    text-align: center;
  }
  
  .sub {
    font-size: 20px;
    font-weight: bold;
    color: white;
    margin-bottom: 10px;
    text-align: center;
  }
  
  .content-image {
    width: 100%;
    max-width: 400px;
    object-fit: contain;
  }
  
  .header {
    display: flex;
  }
  
  .dots-navigation {
  position: fixed;
  top: 50%;
  right: 12px;
  transform: translateY(-50%);
  display: flex;
  flex-direction: column;
  gap: 25px; 
  z-index: 10;
}
.dots {
  display: flex;
  flex-direction: column;
  gap: 20px; 
  align-items: center;
}
.dot {
  width: 12px;
  height: 12px;
  background-color: red; 
  border-radius: 50%;
  opacity: 0.6;
  transition: all 0.3s ease;
}

.dot.active {
  background-color: red;
  opacity: 1;
  transform: scale(1.5);
}

  
  .sticky-btn-container {
    position: fixed;
    bottom: 40px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 20;
  }
  
  .download-btn {
    padding: 10px 30px;
    font-size: 16px;
    border-radius: 50px;
    width: 300px;
    font-weight: bold;
    height: 50px;
    background-color: #d8d8d8;
  }
  </style>
  
  