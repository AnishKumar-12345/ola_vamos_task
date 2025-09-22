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
      <div  :class="['content-wrapper', { 'blur-background': showDownloadDialog }]">
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
            <v-btn class="download-btn" dark @click="handleDownloadClick">立即下载</v-btn>

        </div>
    </div>
      </v-sheet>


      <div class="tutorial-wrapper" v-if="showDownloadDialog && isIOS">
  <div
    class="tutorial-scroll"
    ref="tutorialContainer"
    @scroll="handleTutorialScroll"
  >
    <div
      v-for="(screen, i) in screens"
      :key="i"
      class="tutorial-screen"
    >
      <img :src="screen.bg" alt="screen" class="tutorial-image" />


<div class="tutorial-top-buttons">
  <div v-if="currentStep > 0" class="previous-btn" @click="prevStep">
    <img :src="previous" alt="Previous" class="previous-img" />
    <span class="previous-label">上一步</span>
  </div>


  <div v-else class="button-placeholder"></div>

  <div class="skip-btn" @click="finishTutorial">
    <div class="close-wrapper">
      <img :src="close" alt="close" class="close-img" />
      <span class="close-txt">跳过</span>
    </div>
  </div>
</div>


      <div class="highlight-wrapper" :style="screen.highlightStyle">
        <img :src="screen.highlight" class="highlight-circle" />
       
        <div class="counter-circle">
          <img :src="circle" alt="circle" class="circle-img" />
          <span class="counter-text">{{ i + 1 }} / {{ screens.length }}</span>
        </div>

        <span class="instruction-box">{{ screen.text }}</span>
      </div>
    </div>
  </div>

  <div class="tutorial-bottom-button" @click="handleTutorialButtonClick">
  {{ currentStep === screens.length - 1 ? '完成' : '下一步' }}
</div>
</div>



      <v-dialog
  v-model="showDownloadDialog"
  persistent
  width="320"
  transition="dialog-transition"
>
  <v-card
    class="pa-5"
    elevation="0"
    :style="{
    borderRadius: '60px',
    backgroundImage: `url(${popup_bg})`,
    backgroundSize: 'cover',
    backgroundPosition: 'center',
    backdropFilter: 'blur(25px)',
    WebkitBackdropFilter: 'blur(25px)',
    textAlign: 'center'
  }"
  >
   
    <v-btn
      icon
      size="small"
      class="ma-0 pa-0"
      style="position: absolute; top: 10px; right: 10px; color: white; background-color: transparent;"
      @click="showDownloadDialog = false"
    >
      <v-icon size="20">mdi-close</v-icon>
    </v-btn>

  
    <v-img
      :src="logoImg"
      contain
      width="40"
      height="40"
      class="mb-3 mx-auto"
    ></v-img>

 
    <div class="text-white text-subtitle-1 font-weight-bold mb-2">
      下载提醒
    </div>


    <div
      class="text-white text-body-2 mb-5"
      style="line-height: 1.6; font-size: 14px;"
    >
      请正常下载安装包进行安装，若是华为鸿蒙系统手机请关闭纯净系统进行下载。
    </div>

 
    <v-btn
      variant="text"
      style="color: white; font-weight: bold;"
      @click="showDownloadDialog = false"
    >
      确定
    </v-btn>
  </v-card>
</v-dialog>

    </v-app>
  </template>
  
  
  <script setup>
  import { ref, onMounted, onBeforeUnmount, computed, nextTick  } from 'vue';
  import backgroundImage from '../assets/background.png';
  import logoImg from '../assets/logo.png';
  import screen1 from '../assets/screen 1.png';
  import screen2 from '../assets/screen 2.png';
  import screen3 from '../assets/screen 3.png';
  import screen4 from '../assets/screen 4.png';
  import screen5 from '../assets/screen 5.png';
  import screen6 from '../assets/screen 6.png';
  import screen7 from '../assets/screen 7.png';
  import dscreen1 from '../assets/ios-1.png';
  import dscreen2 from '../assets/ios-2.png';
  import dscreen3 from '../assets/ios-3.png';
  import dscreen4 from '../assets/ios-4.png';
  import dscreen5 from '../assets/ios-5.png';
  import circle from '../assets/circle.png';
  import close from '../assets/close.png';
  import previous from '../assets/previous.png';
  import popup_bg from '../assets/popup_bg.png'
  import highlightcircle from '../assets/messages_banner.png'


  
  const currentIndex = ref(0);
  const scrollContainer = ref(null);

let scrollTimeout = null; 
  const slides = [
    { image: screen1, title: '小鱼扑克 真人视频竞技', subtitle: '创新玩法 乐趣翻倍' },
    { image: screen2, title: '小鱼扑克 真人视频竞技', subtitle: '安全卫士巡航 护卫你的牌桌' },
    { image: screen3, title: '小鱼扑克 真人视频竞技', subtitle: '多种对战玩法实时竞技' },
    { image: screen4, title: '小鱼扑克 真人视频竞技', subtitle: '智能匹配系统 快速开局' },
    { image: screen5, title: '小鱼扑克 真人视频竞技', subtitle: '公平公正 绿色安全' },
    { image: screen6, title: '小鱼扑克 真人视频竞技', subtitle: '好友组局 私密安全' },
    { image: screen7, title: '小鱼扑克 真人视频竞技', subtitle: '好友组局 私密安全' },
  ];

  const tutorialContainer = ref(null);

function handleTutorialScroll() {
  const container = tutorialContainer.value;
  if (!container) return;

  const scrollTop = container.scrollTop;
  const containerHeight = container.clientHeight;

  let activeIndex = 0;
  let minDiff = Infinity;

  screens.forEach((_, i) => {
    const slide = container.children[i];
    const slideTop = slide.offsetTop;
    const diff = Math.abs(slideTop - scrollTop);
    if (diff < minDiff) {
      minDiff = diff;
      activeIndex = i;
    }
  });

  currentStep.value = activeIndex;
}
  const currentStep = ref(0);

const screens = [
  {
    bg: dscreen1,
    // counter:circle,
    highlight: highlightcircle,
    highlightStyle: { top: "20%", left: "10%" },
    text: "点击安装应用"
  },
  {
    bg: dscreen2,
    // counter:circle,
    highlight:highlightcircle,
    highlightStyle: { top: "18%", left: "10%" },
    text: "返回设置选择【通用】"
  },
  {
    bg: dscreen3,
    // counter:circle,
    highlight:highlightcircle,
    highlightStyle: { top: "20%", left: "10%" },
    text: "点击【VPN与设备管理】"
  },
  {
    bg: dscreen4,
    // counter:circle,
    highlight:highlightcircle,
    highlightStyle: {top: "20%", left: "10%" },
    text: "点击【VPN与设备管理】"
  },
  {
    bg: dscreen5,
    // counter:circle,
    highlight:highlightcircle,
    highlightStyle: { top: "20%", left: "10%" },
    text: "点击【VPN与设备管理】"
  },
];
const isIOS = computed(() => /iphone|ipad|ipod/i.test(navigator.userAgent));



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

const showDownloadDialog = ref(false);


function nextStep() {
  if (currentStep.value < screens.length - 1) currentStep.value++;
}

function prevStep() {
  if (currentStep.value > 0) {
    currentStep.value--;
    scrollToTutorialStep(currentStep.value);
  }
}

function scrollToTutorialStep(index) {
  const container = tutorialContainer.value;
  const slide = container.children[index];
  if (slide) {
    container.scrollTo({
      top: slide.offsetTop,
      behavior: 'smooth',
    });
  }
}


function finishTutorial() {
  currentStep.value = 0;
  showDownloadDialog.value = false;

  // alert("Tutorial Finish");
}

function handleTutorialButtonClick() {
  if (currentStep.value === screens.length - 1) {
    finishTutorial();
  } else {
    currentStep.value++;
    scrollToTutorialStep(currentStep.value);
  }
}

function handleDownloadClick() {
  const isAndroid = /android/i.test(navigator.userAgent);
  const isIOSDevice = /iphone|ipad|ipod/i.test(navigator.userAgent);

  if (isAndroid) {
    showDownloadDialog.value = true;
  } else if (isIOSDevice) {
    currentStep.value = 0;
    showDownloadDialog.value = true;
    nextTick(() => {
      scrollToTutorialStep(0);
    });
  } else {
    showDownloadDialog.value = true;
  }
}

  </script>
  
  <style scoped>
  .tutorial-scroll {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  scroll-snap-type: y mandatory;
}

.tutorial-screen {
  width: 100%;
  height: 100vh;             
  scroll-snap-align: start; 
  position: relative;
}


 .tutorial-wrapper {
  position: fixed;   
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: black;
  overflow: hidden;
  z-index: 9999;      
}


.tutorial-bg {
  width: 100%;
  height: 100%;
}

/* .tutorial-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
} */

.step-indicator {
  position: absolute;
  top: 10%;
  left: 20px;
  /* background: red; */
  color: #fff;
  padding: 6px 14px;
  border-radius: 20px;
  font-weight: bold;
}


.highlight-wrapper {
  position: absolute;
  pointer-events: none;
}


.highlight-circle {
  width: 300px;
  pointer-events: none;
  display: block;
}

.counter-circle {
  position: absolute;
  top: 10%; 
  /* left: 100%;   */
  transform: translateX(10px); 
  display: flex;
  align-items: center;
  justify-content: center;
  pointer-events: none;
}
/* .skip-btn{
  position: absolute;
  margin-top: -50%;
  left: 65%;
  pointer-events: auto;
  cursor: pointer;

} */

.circle-img {
  width: 60px;
  height: 60px;
}
.close-wrapper {
  position: relative;
  display: inline-block;
}

.close-img {
  width: 80px;
  height: 35px;
  display: block;
}

.close-txt {
  position: absolute;
  top: 50%;
  left: 40%;
  transform: translate(-50%, -50%); 
  color: white;                     
  font-size: 14px;
  font-weight: bold;
}
.counter-text {
  position: absolute;
  font-size: 14px;
  font-weight: bold;
  color: white;
}


.instruction-box {
  position: absolute;
  top:25%;
  left:25%;
  /* text-align: center; */
  /* bottom: 120px; */
  /* left: 50%; */
  /* transform: translateX(-50%); */
  /* background: red; */
  color: #fff;
  padding: 12px 18px;
  border-radius: 25px;
  font-size: 14px;
}



/* .skip-btn {
  position: absolute;
  top: 15px;
  right: 15px;
  color: white;
  background: transparent;
} */
  .blur-background {
  filter: blur(10px);
  transition: filter 0.3s ease;
  pointer-events: none; /* Optional: prevents interaction behind dialog */
}
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
  scroll-snap-type: y mandatory;
  margin: 0;      
  padding: 0;      
}

.content-card {
  height: 100vh;
  scroll-snap-align: start;
  flex-shrink: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 0;     
  padding: 0;     
  box-sizing: border-box;
}
  
  .logo {
    width: 200px;
    /* margin-bottom: 10px; */
  }
  
  .headline {
  font-size: 20px;
  font-weight: bold;
  color: white;
  margin-top: 10px;
  text-align: center;
  white-space: normal; 
  word-break: break-word;
  max-width: 150px; 
  line-height: 1.3;
}
  .sub {
    font-size: 20px;
    font-weight: bold;
    color: white;
    margin-bottom: 10px;
    text-align: center;
  }
  
  /* .content-image {
    width: 100%;
    max-width: 400px;
    object-fit: contain;
  } */
  
  .header {

  display: flex;
  align-items: center;
  gap: 8px;
  flex-wrap: wrap; 
  justify-content: center;
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
  /* .previous-btn {
  position: absolute;
  margin-top: 20%;
  left: 5%;
  display: flex;
  align-items: center;
  cursor: pointer;
  z-index: 1000;
}

.previous-img {
  width: 80px;
  height: 35px;
  margin-right: 6px;
} */

/* .previous-label {
  position: absolute;
  top: 50%;
  left: 55%;
  transform: translate(-50%, -50%); 
  color: white;                     
  font-size: 10px;
  font-weight: bold;
} */
.tutorial-bottom-button {
  position: absolute;
  bottom: 40px;
  left: 50%;
  width: 300px;
  transform: translateX(-50%);
  background-color: white;
  color: black;
  font-weight: bold;
  border-radius: 25px;
  padding: 10px 30px;
  font-size: 16px;
  cursor: pointer;
  z-index: 1000;
  text-align: center;
}

.tutorial-top-buttons {
  position: absolute;
  top: 8%;
  left: 0;
  width: 100%;
  display: flex;
  justify-content: space-between;
  padding: 0 24px;
  z-index: 1000;
  pointer-events: auto;
}

.button-placeholder {
  width: 100px;
  height: 1px;
}

.previous-btn {
  display: flex;
  align-items: center;
  cursor: pointer;
  position: relative;
  z-index: 1000;
}

.previous-img {
  width: 80px;
  height: 35px;
  display: block;
  position: relative;
}
.previous-label {
  position: absolute;
  top: 50%;
  left: 60%;
  transform: translate(-50%, -50%);
  color: white;
  font-size: 14px; 
  font-weight: bold;
  white-space: nowrap;     
  overflow: hidden;
  text-overflow: ellipsis;  
  width: 100%;            
  text-align: center;      
  pointer-events: none;      
}

.skip-btn {
  display: flex;
  align-items: center;
  cursor: pointer;
  z-index: 1000;
}

.v-application,
.v-application--wrap,
.v-sheet {
  margin: 0 !important;
  padding: 0 !important;
  height: 100vh;
}

.content-wrapper,
.tutorial-scroll {
  height: 100vh;
  overflow-y: auto;
  scroll-snap-type: y mandatory;
  margin: 0;
  padding: 0;
}

.tutorial-wrapper,
.v-application,
.v-sheet {
  margin: 0;
  padding: 0;
  height: 100vh;
  width: 100vw;
}
.content-card,
.tutorial-screen {
  width: 100%;       
  max-width: 500px;      
  margin: 0 auto;        
  height: 100vh;
  scroll-snap-align: start;
  box-sizing: border-box;
  padding: 0 16px;     
}

.tutorial-image,
.content-image {
  width: 100%;    
  height: auto;      
  object-fit: contain; 
  display: block;
  margin: 0 auto;    
}


  </style>
  
  