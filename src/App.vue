<template>
  <Loading />
  <Background @loadComplete="loadComplete" />
  
  <div 
    class="mobile-media-btn" 
    v-show="!showMediaModal && !store.backgroundShow" 
    @click="showMediaModal = true"
  >
    <VideoTwo theme="filled" size="24" fill="#ffffff" />
    <span class="text">影音中心</span>
  </div>

  <Transition name="fade" mode="out-in">
    <main id="main" v-if="store.imgLoadStatus">
      <div class="container" v-show="!store.backgroundShow">
        <section class="all" v-show="!store.setOpenState">
          <MainLeft />
          <MainRight v-show="!store.boxOpenState" />
          <Box v-show="store.boxOpenState" />
        </section>
        <section class="more" v-show="store.setOpenState" @click="store.setOpenState = false">
          <MoreSet />
        </section>
      </div>

      <MediaModal v-model:visible="showMediaModal" />

      <Icon
        class="menu"
        size="24"
        v-show="!store.backgroundShow"
        @click="store.mobileOpenState = !store.mobileOpenState"
      >
        <component :is="store.mobileOpenState ? CloseSmall : HamburgerButton" />
      </Icon>

      <Transition name="fade" mode="out-in">
        <Footer class="f-ter" v-show="!store.backgroundShow && !store.setOpenState" />
      </Transition>
    </main>
  </Transition>
</template>

<script setup>
import { ref, watch, onMounted, onBeforeUnmount, nextTick } from "vue";
import { helloInit, checkDays } from "@/utils/getTime.js";
import { HamburgerButton, CloseSmall, VideoTwo } from "@icon-park/vue-next";
import { mainStore } from "@/store";
import { Icon } from "@vicons/utils";
import { ElMessage } from "element-plus";
import Loading from "@/components/Loading.vue";
import MainLeft from "@/views/Main/Left.vue";
import MainRight from "@/views/Main/Right.vue";
import Background from "@/components/Background.vue";
import Footer from "@/components/Footer.vue";
import Box from "@/views/Box/index.vue";
import MoreSet from "@/views/MoreSet/index.vue";
import MediaModal from "@/components/MediaModal.vue";
import cursorInit from "@/utils/cursor.js";
import config from "@/../package.json";

const store = mainStore();
const showMediaModal = ref(false);

// 获取窗口宽度
const getWidth = () => {
  store.setInnerWidth(window.innerWidth);
};

// 资源加载完成
const loadComplete = () => {
  nextTick(() => {
    helloInit();
    checkDays();
  });
};

// 监听宽度变化
watch(
  () => store.innerWidth,
  (value) => {
    if (value < 721) {
      store.boxOpenState = false;
      store.setOpenState = false;
    }
  },
);

onMounted(() => {
  cursorInit();
  
  // 屏蔽右键
  document.oncontextmenu = () => {
    ElMessage({
      message: "为了浏览体验，本站禁用右键",
      grouping: true,
      duration: 2000,
    });
    return false;
  };

  // 监听尺寸
  getWidth();
  window.addEventListener("resize", getWidth);

  // 控制台字符画
  const title1 = "無名の主页";
  const title2 = `
 █████  ██   ██ ███████ 
██   ██ ██  ██  ██      
███████ █████   █████   
██   ██ ██  ██  ██      
██   ██ ██   ██ ███████`;
  console.info(`%c${title1} %c${title2}`, "font-size: 20px;color: #f4a759;", "font-size: 12px;color: #f4a759;");
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", getWidth);
});
</script>

<style lang="scss" scoped>
.mobile-media-btn {
  display: none;
  @media (max-width: 721px) {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    position: fixed;
    top: 20px;
    left: 20px;
    z-index: 100;
    padding: 8px;
    background: rgba(0, 0, 0, 0.2);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 12px;
    cursor: pointer;
    transition: transform 0.2s, background 0.2s;
    
    .text {
      color: #fff;
      font-size: 10px;
      margin-top: 4px;
      opacity: 0.9;
    }
    
    &:active {
      transform: scale(0.95);
      background: rgba(0, 0, 0, 0.4);
    }
  }
}

#main {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  transform: scale(1.2);
  transition: transform 0.3s;
  animation: fade-blur-main-in 0.65s cubic-bezier(0.25, 0.46, 0.45, 0.94) forwards;
  animation-delay: 0.5s;
  
  .container {
    width: 100%;
    height: 100vh;
    .all {
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
    }
  }

  .menu {
    position: absolute;
    top: 84%;
    left: calc(50% - 28px);
    width: 56px;
    height: 34px;
    background: rgb(0 0 0 / 20%);
    backdrop-filter: blur(10px);
    border-radius: 6px;
    @media (min-width: 721px) {
      display: none;
    }
  }
}
</style>
