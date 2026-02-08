<template>
  <Transition name="fade">
    <div v-if="visible" class="modal-mask" @click.self="closeModal">
      <div class="modal-container" @wheel.prevent="handleWheel">
        <div class="modal-header">
          <span>美女小姐姐 (滚动或点击视频切换)</span>
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
          <div class="tips">PC滚轮切换 | 移动端点击视频切换</div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { ref, watch } from 'vue';
import { CloseOne } from "@icon-park/vue-next";
import { ElMessage } from "element-plus";

const props = defineProps({
  visible: Boolean
});

const emit = defineEmits(['update:visible']);

const videoUrl = ref("");
const isThrottled = ref(false); // 节流开关
const apiUrl = "https://api.yujn.cn/api/zzxjj.php?type=video";

// 切换视频
const refreshVideo = () => {
  videoUrl.value = ""; 
  // 延迟加载确保 DOM 刷新
  setTimeout(() => {
    videoUrl.value = `${apiUrl}&t=${new Date().getTime()}`;
  }, 50);
};

// 处理 PC 滚轮
const handleWheel = (event) => {
  if (event.deltaY > 0 && !isThrottled.value) {
    isThrottled.value = true;
    refreshVideo();
    // 1.5秒后再允许滚轮切换，防止刷接口
    setTimeout(() => {
      isThrottled.value = false;
    }, 1500);
  }
};

// 报错处理
const handleError = () => {
  ElMessage.error("当前视频源失效，正在自动切换");
  refreshVideo();
};

// 关闭逻辑
const closeModal = () => {
  videoUrl.value = "";
  emit('update:visible', false);
};

// 监听显示状态
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
  background: rgba(0, 0, 0, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  backdrop-filter: blur(15px);
}

.modal-container {
  width: 95%;
  max-width: 500px;
  height: 85vh;
  background: #000;
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  position: relative;

  /* 移动端满屏逻辑 */
  @media (max-width: 721px) {
    width: 100vw;
    height: 100vh;
    max-width
