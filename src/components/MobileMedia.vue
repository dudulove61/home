<template>
  <div class="mobile-only-wrapper" v-if="isMobile">
    <div 
      class="m-media-btn" 
      v-show="!showModal" 
      @click="showModal = true"
    >
      <video-two theme="filled" size="24" fill="#ffffff" />
      <span class="text">影音中心</span>
    </div>

    <Teleport to="body">
      <Transition name="m-fade">
        <div v-if="showModal" class="m-modal-mask">
          <div class="m-modal-container">
            <div class="m-header">
              <span>影音中心</span>
              <close-one class="m-close" @click="closeModal" />
            </div>
            <div class="m-content">
              <video 
                autoplay 
                controls 
                class="m-video"
                :src="videoUrl"
                @ended="refreshVideo"
              ></video>
              <button class="m-refresh-btn" @click="refreshVideo">换一个</button>
            </div>
          </div>
        </div>
      </Transition>
    </Teleport>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { VideoTwo, CloseOne } from "@icon-park/vue-next";

const isMobile = ref(false);
const showModal = ref(false);
const videoUrl = ref("");
const apiUrl = "https://api.yujn.cn/api/zzxjj.php?type=video";

const checkMobile = () => {
  isMobile.value = window.innerWidth <= 721;
};

const refreshVideo = () => {
  videoUrl.value = `${apiUrl}&t=${Date.now()}`;
};

const closeModal = () => {
  showModal.value = false;
  videoUrl.value = "";
};

watch(showModal, (val) => {
  if (val) refreshVideo();
});

onMounted(() => {
  checkMobile();
  window.addEventListener('resize', checkMobile);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', checkMobile);
});
</script>

<style lang="scss" scoped>
.m-media-btn {
  position: fixed;
  bottom: 120px; /* 放在菜单按钮上方，避免重叠 */
  right: 20px;
  z-index: 999;
  width: 60px;
  height: 60px;
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(10px);
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 4px 12px rgba(0,0,0,0.3);
  .text { color: #fff; font-size: 9px; margin-top: 2px; }
}

.m-modal-mask {
  position: fixed;
  top: 0; left: 0; width: 100vw; height: 100vh;
  background: #000;
  z-index: 2000;
  display: flex; flex-direction: column;
}

.m-modal-container {
  height: 100%; display: flex; flex-direction: column;
  .m-header {
    padding: 15px; display: flex; justify-content: space-between;
    background: #111; color: #eee;
    .m-close { font-size: 24px; }
  }
  .m-content {
    flex: 1; position: relative; background: #000;
    display: flex; align-items: center; justify-content: center;
    .m-video { width: 100%; height: 100%; object-fit: contain; }
    .m-refresh-btn {
      position: absolute; bottom: 30px;
      padding: 10px 30px; background: #3498db; color: white;
      border: none; border-radius: 20px; font-size: 14px;
    }
  }
}

.m-fade-enter-active, .m-fade-leave-active { transition: all 0.4s ease; }
.m-fade-enter-from, .m-fade-leave-to { opacity: 0; transform: translateY(100px); }
</style>
