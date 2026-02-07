<template>
  <div class="more-content">
    <div class="media-entry-card" @click="showModal = true">
      <div class="icon-circle">
        <video-two theme="two-tone" size="30" :fill="['#efefef', '#ff4d4f']" />
      </div>
      <div class="info">
        <span class="title">影音中心</span>
        <span class="desc">发现更多精彩内容</span>
      </div>
    </div>

    <MediaModal v-model:visible="showModal" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import { VideoTwo } from "@icon-park/vue-next";
import MediaModal from "@/components/MediaModal/index.vue"; // 引入刚才建的文件

const showModal = ref(false);
</script>

<style lang="scss" scoped>
.more-content {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  padding-top: 20px;

  .media-entry-card {
    width: 85%;
    background: rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.2);
    border-radius: 12px;
    padding: 12px 20px;
    display: flex;
    align-items: center;
    gap: 15px;
    cursor: pointer;
    backdrop-filter: blur(10px);
    transition: all 0.3s ease;

    .icon-circle {
      background: rgba(255, 255, 255, 0.05);
      padding: 10px;
      border-radius: 50%;
      display: flex;
    }

    .info {
      display: flex;
      flex-direction: column;
      .title { color: #fff; font-size: 16px; font-weight: bold; }
      .desc { color: rgba(255, 255, 255, 0.5); font-size: 12px; }
    }

    &:hover {
      background: rgba(255, 255, 255, 0.15);
      border-color: #ff4d4f;
      transform: translateY(-5px);
      box-shadow: 0 10px 20px rgba(255, 77, 79, 0.2);
      .title { color: #ff4d4f; }
    }
  }
}
</style>
