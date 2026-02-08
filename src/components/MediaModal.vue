<template>
  <Teleport to="body">
    <Transition name="fade">
      <div v-if="visible" class="modal-mask" @click.self="closeModal">
        <div class="modal-container" @wheel.prevent="handleWheel">
          <div class="modal-header">
            <span>影音中心</span>
            <close-one class="close-icon" @click="closeModal" />
          </div>
          <div class="modal-body">
            <video 
              ref="videoPlayer"
              class="video-content" 
              controls 
              autoplay
              :src="videoUrl"
              @ended="refreshVideo"
              @click="refreshVideo"
              @error="handleError"
            ></video>
            <div class="btn-group">
              <el-button type="primary" round @click.stop="refreshVideo">换一个</el-button>
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
  ElMessage.error("视频加载失败，自动换一个");
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
.modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
  backdrop-filter: blur(15px);
}

.modal-container {
  width: 90%;
  max-width: 450px;
  height: 80vh;
  background: #000;
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  position: relative;

  @media (max-width: 721px) {
    width: 100vw;
    height: 100vh;
    max-width: none;
    border-radius: 0;
    border: none;
  }
}

.modal-header {
  padding: 10px 15px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #111;
  color: #888;
  font-size: 12px;
  .close-icon { cursor: pointer; &:hover { color: #ff4d4f; } }
}

.modal-body {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: #000;
  .video-content { width: 100%; height: 100%; object-fit: contain; }
  .btn-group { position: absolute; bottom: 40px; }
}

.fade-enter-active, .fade-leave-active { transition: opacity 0.3s ease; }
.fade-enter-from, .fade-leave-to { opacity: 0; }
</style>
