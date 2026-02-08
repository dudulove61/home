<template>
  <Transition name="fade">
    <div v-if="visible" class="modal-mask" @click.self="$emit('update:visible', false)">
      <div class="modal-container">
        <div class="modal-header">
          <span>影音中心</span>
          <close-one class="close-icon" @click="$emit('update:visible', false)" />
        </div>
        <div class="modal-body">
          <video 
            ref="videoPlayer"
            class="video-content" 
            controls 
            autoplay
            :src="videoUrl"
          ></video>
          <div class="btn-group">
            <el-button type="primary" round @click="refreshVideo">换一个</el-button>
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';
import { CloseOne } from "@icon-park/vue-next";

const props = defineProps({
  visible: Boolean
});

const emit = defineEmits(['update:visible']);

const videoUrl = ref("");
const videoPlayer = ref(null);

// 视频接口地址
const apiUrl = "https://api.yujn.cn/api/zzxjj.php?type=video";

const refreshVideo = () => {
  // 加上时间戳防止缓存，确保每次请求都是新视频
  videoUrl.value = `${apiUrl}&t=${new Date().getTime()}`;
};

// 当窗口打开时，自动加载视频
watch(() => props.visible, (val) => {
  if (val) {
    refreshVideo();
  } else {
    // 关闭时停止播放并清空，节省流量
    videoUrl.value = "";
  }
});

</script>

<style lang="scss" scoped>
.modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
  backdrop-filter: blur(5px);
}

.modal-container {
  width: 90%;
  max-width: 800px;
  background: rgba(30, 30, 30, 0.9);
  border-radius: 16px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  overflow: hidden;
}

.modal-header {
  padding: 15px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: #fff;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  .close-icon {
    cursor: pointer;
    &:hover { color: #ff4d4f; }
  }
}

.modal-body {
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  
  .video-content {
    width: 100%;
    max-height: 60vh;
    border-radius: 8px;
    background: #000;
  }

  .btn-group {
    margin-top: 20px;
  }
}

/* 动画 */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>
