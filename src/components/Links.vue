<template>
  <div v-if="siteLinks && siteLinks.length > 0" class="links">
    <div class="line">
      <Icon size="20">
        <Link />
      </Icon>
      <span class="title">网站列表</span>
    </div>
    <Swiper
      :modules="[Pagination, Mousewheel]"
      :slides-per-view="1"
      :space-between="40"
      :pagination="{
        el: '.swiper-pagination',
        clickable: true,
        bulletElement: 'div',
      }"
      :mousewheel="true"
    >
      <SwiperSlide v-for="(site, pageIndex) in siteLinksList" :key="pageIndex">
        <el-row class="link-all" :gutter="20">
          <el-col v-for="(item, index) in site" :span="8" :key="item.link">
            <div
              class="item cards"
              :style="index < 3 ? 'margin-bottom: 20px' : null"
              @click="jumpLink(item)"
            >
              <Icon size="26">
                <component :is="siteIcon[item.icon] || Link" />
              </Icon>
              <span class="name text-hidden">{{ item.name }}</span>
            </div>
          </el-col>
        </el-row>
      </SwiperSlide>
      <div class="swiper-pagination" />
    </Swiper>
  </div>
</template>

<script setup>
import { computed, onMounted } from "vue";
import { Icon } from "@vicons/utils";
// 引入 Font Awesome 图标库中存在的图标
import { 
  Link, 
  Blog, 
  CompactDisc, 
  Cloud, 
  Compass, 
  Book, 
  Fire, 
  LaptopCode,
  Tv as LiveTvFilled, 
  Telegram
} from "@vicons/fa";
import { mainStore } from "@/store";
import { Swiper, SwiperSlide } from "swiper/vue";
import { Pagination, Mousewheel } from "swiper/modules";
import siteLinks from "@/assets/siteLinks.json";

// 必须引入 Swiper 样式才能正常显示
import "swiper/css";
import "swiper/css/pagination";

const store = mainStore();

// 计算网站链接：每页显示 6 个
const siteLinksList = computed(() => {
  const result = [];
  if (!siteLinks) return result;
  for (let i = 0; i < siteLinks.length; i += 6) {
    const
