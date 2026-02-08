<template>
  <Transition name="fade">
    <div v-if="visible" class="modal-mask" @click.self="closeModal">
      <div class="modal-container" @wheel.prevent="handleWheel">
        <div class="modal-header">
          <span>影音中心 (点击视频或滚动切换)</span>
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
          <div class="tips">PC滚轮下划 | 移动端点击视频切换</div>
        </div>
      </div>
    </div>
  </Transition>
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

// 刷新视频源
const refreshVideo = () => {
  videoUrl.value = ""; 
  setTimeout(() => {
    videoUrl.value = `${apiUrl}&t=${new Date().getTime()}`;
  }, 60);
};

// PC滚轮逻辑
const handleWheel = (event) => {
  if (event.deltaY > 0 && !isThrottled.value) {
    isThrottled.value = true;
    refreshVideo();
    setTimeout(() => { isThrottled.value = false; }, 1200);
  }
};

const handleError = () => {
  ElMessage.error("视频加载失败，尝试自动切换");
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
  top: 0; left: 0; width: 100%; height: 100%;
  background: rgba(0, 0, 0, 0.9);
  display: flex; align-items: center; justify-content: center;
  z-index: 1000; backdrop-filter: blur(15px);
}

.modal-container {
  width: 95%; max-width: 500px; height: 85vh;
  background: #000; border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  overflow: hidden; display: flex; flex-direction: column;
  box-shadow: 0 10px 50px rgba(0,0,0,0.8);

  /* 移动端全屏覆盖逻辑 */
  @media (max-width: 721px) {
    width: 100vw; height: 100vh; max-width: none; border-radius: 0; border: none;
  }
}

.modal-header {
  padding: 12px 20px; display: flex; justify-content: space-between;
  align-items: center; color: #777; font-size: 12px;
  background: rgba(255,255,255,0.03); z-index: 10;
  .close-icon { cursor: pointer; font-size: 20px; &:hover { color: #ff4d4f; } }
}

.modal-body {
  flex: 1; position: relative; display: flex;
  flex-direction: column; align-items: center; justify-content: center;
  background: #000;

  .video-content {
    width: 100%; height: 100%; 
    object-fit: contain; // 保持比例，避免拉伸变形
  }

  .btn-group { position: absolute; bottom: 60px; z-index: 11; opacity: 0.8; }
  .tips { position: absolute; bottom: 25px; font-size: 10px; color: rgba(255,255,255,0.3); z-index: 11; }
}

.fade-enter-active, .fade-leave-active { transition: all 0.3s ease-out; }
.fade-enter-from, .fade-leave-to { opacity: 0; transform: scale(0.95); }
</style>
