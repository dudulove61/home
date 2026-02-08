<template>
  <Loading />
  <Background @loadComplete="loadComplete" />
  
  <MediaModal v-model:visible="showMediaModal" />

  <Transition name="fade" mode="out-in">
    <main id="main" v-if="store.imgLoadStatus">
      
      <div 
        class="mobile-media-btn" 
        v-show="!showMediaModal && !store.backgroundShow" 
        @click="showMediaModal = true"
      >
        <video-two theme="filled" size="24" fill="#ffffff" />
        <span class="text">影音中心</span>
      </div>

      <div class="container" v-show="!store.backgroundShow">
        <section class="all" v-show="!store.setOpenState">
          <MainLeft />
          <MainRight v-show="!store.boxOpenState" />
          <Box v-show="store.boxOpenState" />
        </section>
        <section class="more" v-show="store.setOpenState" @click="store.setOpenState = false">
          <MoreSet />
        </section>
      </div>

      <Icon
        class="menu"
        size="24"
        v-show="!store.backgroundShow"
        @click="store.mobileOpenState = !store.mobileOpenState"
      >
        <component :is="store.mobileOpenState ? CloseSmall : HamburgerButton" />
      </Icon>

      <Transition name="fade" mode="out-in">
        <Footer class="f-ter" v-show="!store.backgroundShow && !store.setOpenState" />
      </Transition>
    </main>
  </Transition>
</template>

<style lang="scss" scoped>
/* 严格限制移动端按钮样式，物理隔离 PC 端 */
.mobile-media-btn {
  display: none; 
  @media (max-width: 721px) {
    display: flex;
    flex-direction: column;
    align-items: center;
    position: fixed;
    top: 20px;
    left: 20px;
    z-index: 99;
    padding: 8px;
    background: rgba(0, 0, 0, 0.2);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 12px;
    .text { color: #fff; font-size: 10px; margin-top: 4px; }
  }
}

#main {
  position: absolute;
  top: 0; left: 0; width: 100%; height: 100%;
  transform: scale(1.2); /* 之前的弹窗错位就是因为它 */
  transition: transform 0.3s;
  /* ... 其他原有样式保持不变 ... */
}
</style>
