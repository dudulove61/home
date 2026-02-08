<template>
  <Loading />
  <Background @loadComplete="loadComplete" />
  
  <Transition name="fade" mode="out-in">
    <main id="main" v-if="store.imgLoadStatus">
      
      <div 
        class="mobile-media-btn" 
        v-show="!showMediaModal && !store.backgroundShow" 
        @click="showMediaModal = true"
      >
        <video-two theme="filled" size="24" fill="#ffffff" />
        <span class="text">影音中心</span>
      </div>

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

const getWidth = () => { store.setInnerWidth(window.innerWidth); };
const loadComplete = () => { nextTick(() => { helloInit(); checkDays(); }); };

watch(() => store.innerWidth, (value) => {
  if (value < 721) {
    store.boxOpenState = false;
    store.setOpenState = false;
  }
});

onMounted(() => {
  cursorInit();
  getWidth();
  window.addEventListener("resize", getWidth);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", getWidth);
});
</script>

<style lang="scss" scoped>
/* 核心隔离样式：确保 PC 端完全不可见且不占位 */
.mobile-media-btn {
  display: none; 

  @media (max-width: 721px) {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    position: fixed; /* 必须是 fixed，脱离文档流，不干扰 .all 的居中 */
    top: 20px;
    left: 20px;
    z-index: 99;
    padding: 8px;
    background: rgba(0, 0, 0, 0.2);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 12px;
    cursor: pointer;

    .text {
      color: #fff;
      font-size: 10px;
      margin-top: 4px;
    }
    &:active {
      transform: scale(0.95);
    }
  }
}

#main {
  position: absolute;
  top: 0; left: 0; width: 100%; height: 100%;
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
