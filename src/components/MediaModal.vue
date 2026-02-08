<template>
  <Teleport to="body">
    <Transition name="fade">
      <div v-if="visible" class="media-modal-mask" @click.self="closeModal">
        <div class="media-modal-container" @wheel.prevent="handleWheel">
          <div class="media-modal-header">
            <span>影音中心</span>
            <close-one class="close-icon" @click="closeModal" />
          </div>
          <div class="media-modal-body">
            <video 
              ref="videoPlayer"
              class="video-player-content" 
              controls 
              autoplay
              :src="videoUrl"
              @ended="refreshVideo"
              @click="refreshVideo"
              @error="handleError"
            ></video>
            <div class="control-btn-group">
              <el-button type="primary" round @click.stop="refreshVideo">换一个 (点击视频也可切换)</el-button>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref, watch } from 'vue';
import { CloseOne } from "@icon-park/vue-next";
import { ElMessage } from "element-plus";

const props = defineProps({ visible: Boolean });
const emit = defineEmits(['update:visible']);

const videoUrl = ref("");
const isThrottled = ref(false);
const apiUrl = "https://api.yujn.cn/api/zzxjj.php?type=video";

const refreshVideo = () => {
  videoUrl.value = ""; 
  setTimeout(() => {
    videoUrl.value = `${apiUrl}&t=${new Date().getTime()}`;
  }, 50);
};

const handleWheel = (event) => {
  if (event.deltaY > 0 && !isThrottled.value) {
    isThrottled.value = true;
    refreshVideo();
    setTimeout(() => { isThrottled.value = false; }, 1200);
  }
};

const handleError = () => {
  // 屏蔽频繁报错，自动静默切换
  refreshVideo();
};

const closeModal = () => {
  videoUrl.value = "";
  emit('update:visible', false);
};

watch(() => props.visible, (val) => {
  if (val) refreshVideo();
});
</script>

<style lang="scss" scoped>
/* 蒙版层：强制覆盖全屏，不受父级 transform 影响 */
.media-modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 99999;
  backdrop-filter: blur(20px);
}

.media-modal-container {
  /* PC端展示样式 */
  width: 95%;
  max-width: 400px;
  height: 80vh;
  background: #000;
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  position: relative;

  /* 移动端全屏逻辑 */
  @media (max-width: 721px) {
    width: 100vw !important;
    height: 100vh !important;
    max-width: none !important;
    border-radius: 0 !important;
    border: none !important;
  }
}

.media-modal-header {
  padding: 12px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: rgba(255,255,255,0.05);
  color: #ccc;
  font-size: 13px;
  z-index: 10;
  .close-icon { cursor: pointer; font-size: 20px; &:hover { color: #ff4d4f; } }
}

.media-modal-body {
  flex: 1;
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden; /* 防止视频溢出 */

  .video-player-content {
    width: 100%;
    height: 100%;
    /* 核心：确保视频在容器内按比例缩放，不挤出屏幕 */
    object-fit: contain; 
    background: #000;
  }

  .control-btn-group {
    position: absolute;
    bottom: 50px;
    z-index: 11;
    opacity: 0.7;
    &:hover { opacity: 1; }
  }
}

.fade-enter-active, .fade-leave-active { transition: opacity 0.3s; }
.fade-enter-from, .fade-leave-to { opacity: 0; }
</style>
