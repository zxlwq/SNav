<template>
  <div :class="status.siteStatus !== 'normal' ? 'cover focus' : 'cover'">
    <img
      class="background"
      alt="background"
      :src="bgUrl"
      :style="{ '--blur': set.backgroundBlur + 'px' }"
    />
    <Transition name="fade">
      <div v-if="set.showBackgroundGray" class="gray" />
    </Transition>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { statusStore, setStore } from "@/stores";

const set = setStore();
const status = statusStore();
const bgUrl = ref(null);

// 壁纸随机数
const bgRandom = Math.floor(Math.random() * 3 + 1);

// 赋值壁纸
const setBgUrl = () => {
  const { backgroundType } = set;
  switch (backgroundType) {
    case 0:
      bgUrl.value = `/background/bg4.jpg`;
      break;
    case 1: {
      const isMobile = window.innerWidth < 768;
      bgUrl.value = `https://api.dujin.org/bing/${isMobile ? "m" : "1920"}.php`;
      break;
    }
    case 2:
      bgUrl.value = "https://api.aixiaowai.cn/gqapi/gqapi.php";
      break;
    case 3:
      bgUrl.value = "https://api.aixiaowai.cn/api/api.php";
      break;
    case 4:
      bgUrl.value = set.backgroundCustom;
      break;
    default:
      bgUrl.value = `/background/bg4.jpg`;
      break;
  }
};

onMounted(() => {
  setBgUrl();
});

onBeforeUnmount(() => {
  // 不再需要清理定时器或处理其他加载相关的内容
});
</script>

<style lang="scss" scoped>
.cover {
  width: 100%;
  height: 100%;
  position: relative;
  background-color: var(--body-background-color);
  &.focus {
    .background {
      filter: blur(calc(var(--blur) + 10px)) brightness(0.8);
      transform: scale(1.3);
    }
  }
  .background {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
    backface-visibility: hidden;
    transform: scale(1.2);
    filter: blur(var(--blur));
    transition:
      filter 0.3s,
      transform 0.3s;
    animation: fade-blur-in 1s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  }
  .gray {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-image: radial-gradient(rgba(0, 0, 0, 0) 0, rgba(0, 0, 0, 0.5) 100%),
      radial-gradient(rgba(0, 0, 0, 0) 33%, rgba(0, 0, 0, 0.3) 166%);
  }
}
</style>
