<template>
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
</template>

<style lang="scss" scoped>
.modal-mask {
  position: fixed;
  /* 强制覆盖全屏，不受父级缩放干扰 */
  top: 0 !important;
  left: 0 !important;
  width: 100vw !important;
  height: 100vh !important;
  background: rgba(0, 0, 0, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999; /* 设为最高 */
  backdrop-filter: blur(15px);
}

.modal-container {
  /* PC 端样式 */
  width: 90%;
  max-width: 450px;
  height: 80vh;
  background: #000;
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  position: relative; /* 内部定位 */

  /* 移动端全屏样式 */
  @media (max-width: 721px) {
    width: 100vw;
    height: 100vh;
    max-width: none;
    border-radius: 0;
    border: none;
  }
}
/* ... 其余 CSS 保持不变 ... */
</style>
