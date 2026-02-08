<template>
  <Teleport to="body">
    <Transition name="fade">
      <div 
        v-if="visible" 
        class="media-mask" 
        @click.self="closeModal"
        @touchstart="handleTouchStart"
        @touchend="handleTouchEnd"
      >
        <div class="media-container">
          <div class="media-header">
            <span>影音中心 (向上滑动切换)</span>
            <close-one class="close-icon" @click="closeModal" />
          </div>
          <div class="media-body">
            <video 
              ref="videoPlayer"
              class="video-content" 
              controls 
              autoplay
              muted
              playsinline
              :src="videoUrl"
              @ended="refreshVideo"
              @error="handleError"
            ></video>
            <div class="tips">向上滑动切换视频</div>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref, watch, nextTick } from 'vue';
import { CloseOne } from "@icon-park/vue-next";

const props = defineProps({ visible: Boolean });
const emit = defineEmits(['update:visible']);

const videoPlayer = ref(null);
const videoUrl = ref("");
const apiUrl = "https://api.yujn.cn/api/zzxjj.php?type=video";

// 触摸逻辑
const touchStartY = ref(0);

const handleTouchStart = (e) => {
  touchStartY.value = e.touches[0].clientY;
};

const handleTouchEnd = (e) => {
  const touchEndY = e.changedTouches[0].clientY;
  // 向上滑动距离超过 50 像素则切换
  if (touchStartY.value - touchEndY > 50) {
    refreshVideo();
  }
};

const refreshVideo = () => {
  videoUrl.value = ""; 
  nextTick(() => {
    videoUrl.value = `${apiUrl}&t=${Date.now()}`;
    // 尝试播放
    setTimeout(() => {
      if (videoPlayer.value) {
        videoPlayer.value.muted = false; // 尝试取消静音
        videoPlayer.value.play().catch(() => {
          console.log("静音自动播放已启动");
        });
      }
    }, 100);
  });
};

const handleError = () => { refreshVideo(); };

const closeModal = () => {
  videoUrl.value = "";
  emit('update:visible', false);
};

watch(() => props.visible, (val) => {
  if (val) refreshVideo();
});
</script>

<style lang="scss" scoped>
.media-mask {
  position: fixed;
  top: 0; left: 0; width: 100vw; height: 100vh;
  background: #000;
  z-index: 99999;
  display: flex; align-items: center; justify-content: center;
}
.media-container {
  width: 100%; height: 100%;
  display: flex; flex-direction: column;
}
.media-header {
  padding: 15px; display: flex; justify-content: space-between;
  background: rgba(255,255,255,0.1); color: #fff; font-size: 14px;
  .close-icon { cursor: pointer; font-size: 20px; }
}
.media-body {
  flex: 1; position: relative; background: #000;
  display: flex; align-items: center; justify-content: center;
  .video-content { width: 100%; height: 100%; object-fit: contain; }
  .tips {
    position: absolute; bottom: 20px; color: rgba(255,255,255,0.4);
    font-size: 12px; pointer-events: none;
  }
}
.fade-enter-active, .fade-leave-active { transition: opacity 0.3s; }
.fade-enter-from, .fade-leave-to { opacity: 0; }
</style>
