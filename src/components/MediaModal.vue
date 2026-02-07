<template>
  <Transition name="fade">
    <div class="media-modal-overlay" v-if="visible" @click.self="close">
      <div class="media-modal-content">
        <div class="modal-header">
          <div class="mode-tabs">
            <div :class="['tab', mode === 'movie' ? 'active' : '']" @click="mode = 'movie'">
              <play-two theme="outline" size="18" /> 电影模式
            </div>
            <div :class="['tab', mode === 'video' ? 'active' : '']" @click="mode = 'video'">
              <video-two theme="outline" size="18" /> 短视频
            </div>
          </div>
          <close-one class="close-icon" theme="outline" size="24" @click="close" />
        </div>

        <div class="modal-body">
          <iframe 
            :src="currentUrl" 
            frameborder="0" 
            allowfullscreen 
            allow="autoplay; encrypted-media"
          ></iframe>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { ref, computed } from "vue";
import { CloseOne, PlayTwo, VideoTwo } from "@icon-park/vue-next";

const props = defineProps({
  visible: Boolean
});

const emit = defineEmits(["update:visible"]);

const mode = ref("movie");

// 这里填入你想看的地址
const currentUrl = computed(() => {
  return mode.value === "movie" 
    ? "https://tv.uke.cc/"  // 电影站
    : "https://www.tiktok.com/"; // douyin
});

const close = () => {
  emit("update:visible", false);
};
</script>

<style lang="scss" scoped>
.media-modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
  backdrop-filter: blur(8px);

  .media-modal-content {
    width: 90vw;
    height: 85vh;
    max-width: 1200px;
    background: #1a1a1a;
    border-radius: 16px;
    display: flex;
    flex-direction: column;
    overflow: hidden;
    border: 1px solid rgba(255, 255, 255, 0.1);

    .modal-header {
      padding: 15px 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: #252525;

      .mode-tabs {
        display: flex;
        gap: 20px;
        .tab {
          color: #999;
          cursor: pointer;
          display: flex;
          align-items: center;
          gap: 6px;
          font-size: 14px;
          transition: 0.3s;
          &.active { color: #ff4d4f; font-weight: bold; }
        }
      }
      .close-icon { cursor: pointer; color: #fff; &:hover { color: #ff4d4f; } }
    }

    .modal-body {
      flex: 1;
      background: #000;
      iframe { width: 100%; height: 100%; }
    }
  }
}

.fade-enter-active, .fade-leave-active { transition: opacity 0.3s; }
.fade-enter-from, .fade-leave-to { opacity: 0; }

</style>
