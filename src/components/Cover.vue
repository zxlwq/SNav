<template>
  <div :class="status.siteStatus !== 'normal' ? 'cover focus' : 'cover'">
    <img
      v-show="status.imgLoadStatus"
      class="background"
      alt="background"
      :src="bgUrl"
      :style="{ '--blur': set.backgroundBlur + 'px' }"
      @load="imgLoadComplete"
      @error.once="imgLoadError"
    />
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { statusStore, setStore } from "@/stores";

const set = setStore();
const status = statusStore();
const bgUrl = ref(null);
const imgTimeout = ref(null);
const emit = defineEmits(["loadComplete"]);

// 设置背景图
const setBgUrl = () => {
  const backgroundType = set.backgroundType ?? 1; // 允许 Pinia 配置背景类型
  switch (backgroundType) {
    case 0:
      bgUrl.value = `/background/bg4.jpg`;
      break;
    case 1:
      bgUrl.value = "https://images.zxl.cc.ua/blog/12.webp"; // 自定义壁纸
      break;
    default:
      bgUrl.value = `/background/bg4.jpg`;
      break;
  }
};

// 图片加载完成
const imgLoadComplete = () => {
  clearTimeout(imgTimeout.value);
  imgTimeout.value = setTimeout(() => {
    status.setImgLoadStatus(true);
  }, Math.floor(Math.random() * 301) + 300); // 随机 300~600ms
};

// 图片加载失败，使用备用图片
const imgLoadError = () => {
  console.error("壁纸加载失败，使用默认壁纸");
  bgUrl.value = "/background/bg4.jpg";
};

onMounted(() => {
  setBgUrl();
});

onBeforeUnmount(() => {
  clearTimeout(imgTimeout.value);
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
  }
}
</style>
