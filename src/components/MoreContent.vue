<template>
  <div class="more-content">
    <div class="media-card" @click="showModal = true">
      <video-two theme="two-tone" size="28" :fill="['#efefef', '#ff4d4f']" />
      <span class="text">美女小姐姐</span>
    </div>

    <MediaModal v-model:visible="showModal" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import { VideoTwo } from "@icon-park/vue-next";
// 重点：确保路径直接指向 MediaModal.vue 文件
import MediaModal from "@/components/MediaModal.vue"; 

const showModal = ref(false);
</script>

<style lang="scss" scoped>
.more-content {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 20px;
  width: 100%;
  .media-card {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8px;
    padding: 12px 30px;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.2);
    border-radius: 12px;
    cursor: pointer;
    backdrop-filter: blur(10px);
    &:hover {
      border-color: #ff4d4f;
      transform: translateY(-2px);
    }
    .text { color: #fff; font-size: 14px; }
  }
}
</style>
