<template>
  <Loading />
  <Background @loadComplete="loadComplete" />

  <template v-if="store.innerWidth <= 721">
    <div
      class="mobile-media-entrance"
      v-show="!showMediaModal && !store.backgroundShow"
      @click="showMediaModal = true"
    >
      <video-two theme="filled" size="24" fill="#ffffff" />
      <span class="text">影音中心</span>
    </div>
    <MediaModal v-model:visible="showMediaModal" />
  </template>

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

// 页面宽度检测逻辑
const getWidth = () => {
  store.setInnerWidth(window.innerWidth);
};

// 资源加载完成后的初始化
const loadComplete = () => {
  nextTick(() => {
    helloInit();
    checkDays();
  });
};

// 响应式监听宽度，处理移动端 UI 切换
watch(
  () => store.innerWidth,
  (value) => {
    if (value < 721) {
      store.boxOpenState = false;
      store.setOpenState = false;
    }
  }
);

onMounted(() => {
  // 1. 初始化自定义鼠标
  cursorInit();

  // 2. 屏蔽右键（提升沉浸感）
  document.oncontextmenu = () => {
    ElMessage({
      message: "为了浏览体验，本站禁用右键",
      grouping: true,
      duration: 2000,
    });
    return false;
  };

  // 3. 鼠标中键开启壁纸模式
  window.addEventListener("mousedown", (event) => {
    if (event.button == 1) {
      store.backgroundShow = !store.backgroundShow;
      ElMessage({
        message: `已${store.backgroundShow ? "开启" : "退出"}壁纸展示状态`,
        grouping: true,
      });
    }
  });

  // 4. 监听视口变化
  getWidth();
  window.addEventListener("resize", getWidth);

  // 5. 控制台彩蛋输出
  const styleTitle1 = "font-size: 20px;font-weight: 600;color: rgb(244,167,89);";
  const title1 = "無名の主页";
  console.info(`%c${title1}`, styleTitle1);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", getWidth);
});
</script>

<style lang="scss" scoped>
/* 移动端影音中心按钮样式 
  采用 fixed 定位，完全脱离文档流，
  不会对 PC 端的 #main 或 .container 产生任何挤压
*/
.mobile-media-entrance {
  position: fixed;
  top: 20px;
  left: 20px;
  z-index: 9999; 
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 8px 10px;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.15);
  border-radius: 12px;
  cursor: pointer;
  transition: transform 0.2s, background 0.2s;

  .text {
    color: #fff;
    font-size: 10px;
    margin-top: 4px;
    font-weight: 500;
  }

  &:active {
    transform: scale(0.9);
    background: rgba(0, 0, 0, 0.7);
  }
}

/* 原始 PC 端布局样式 - 请勿修改 */
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
    margin: 0 auto;
    padding: 0 0.5vw;
    .all {
      width: 100%;
      height: 100%;
      padding: 0 0.75rem;
      display: flex;
      flex-direction: row;
      justify-content: center;
      align-items: center;
    }
    .more {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: #00000080;
      backdrop-filter: blur(20px);
      z-index: 2;
      animation: fade 0.5s;
    }
  }

  .menu {
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
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

  /* 针对移动端特殊高度的适配 */
  @media (max-height: 720px) {
    overflow-y: auto;
    overflow-x: hidden;
    .container {
      height: 721px;
      .more { height: 721px; width: calc(100% + 6px); }
    }
  }
}
</style>
