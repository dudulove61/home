<template>
  <Transition name="fade">
    <div v-if="visible" class="modal-mask" @click.self="closeModal">
      <div class="modal-container" @wheel.prevent="handleWheel">
        <div class="modal-header">
          <span>美女小姐姐 (当前状态: {{ scrollHint }})</span>
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
            @error="handleError"
          ></video>
          
          <div class="btn-group">
            <el-button type="primary" round @click="refreshVideo">换一个 (滚轮下划)</el-button>
          </div>
          
          <div class="tips">提示：PC端鼠标向下滚动可切换下一个</div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { ref, watch, onBeforeUnmount } from 'vue';
import { CloseOne } from "@icon-park/vue-next";
import { ElMessage } from "element-plus";

const props = defineProps({
  visible: Boolean
});

const emit = defineEmits(['update:visible']);

const videoUrl = ref("");
const scrollHint = ref("已就绪");
const isThrottled = ref(false); // 节流阀，防止滚轮太快导致连续请求

// 接口地址
const apiUrl = "https://api.yujn.cn/api/zzxjj.php?type=video";

// 切换视频逻辑
const refreshVideo = () => {
  videoUrl.value = ""; // 先清空，触发视频重载
  setTimeout(() => {
    videoUrl.value = `${apiUrl}&t=${new Date().getTime()}`;
    scrollHint.value = "正在加载...";
  }, 50);
};

// 鼠标滚轮处理
const handleWheel = (event) => {
  // event.deltaY > 0 表示向下滚动
  if (event.deltaY > 0 && !isThrottled.value) {
    isThrottled.value = true;
    scrollHint.value = "切换中...";
    refreshVideo();
    
    // 1.5秒节流，防止滚轮划一下触发十几次请求
    setTimeout(() => {
      isThrottled.value = false;
      scrollHint.value = "已就绪";
    }, 1500);
  }
};

// 错误处理
const handleError = () => {
  ElMessage.error("视频加载失败，正在尝试下一个");
  refreshVideo();
};

// 关闭窗口
const closeModal = () => {
  videoUrl.value = "";
  emit('update:visible', false);
};

// 监听打开状态
watch(() => props.visible, (val) => {
  if (val) {
    refreshVideo();
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
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  backdrop-filter: blur(10px);
}

.modal-container {
  width: 95%;
  max-width: 500px; // 既然是短视频 API，竖屏容器更合适
  background: #000;
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  overflow: hidden;
  box-shadow: 0 0 30px rgba(0,0,0,0.5);
}

.modal-header {
  padding: 12px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: #ccc;
  font-size: 13px;
  background: rgba(255,255,255,0.05);
  .close-icon {
    cursor: pointer;
    font-size: 20px;
    &:hover { color: #ff4d4f; }
  }
}

.modal-body {
  padding: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
  
  .video-content {
    width: 100%;
    height: 70vh; // 竖屏比例
    border-radius: 12px;
    object-fit: contain; // 保证视频比例正确
  }

  .btn-group {
    margin: 15px 0;
  }

  .tips {
    font-size: 12px;
    color: #666;
    margin-bottom: 10px;
  }
}

.fade-enter-active, .fade-leave-active {
  transition: all 0.3s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
  transform: scale(0.9);
}
</style>
