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
      @animationend="imgAnimationEnd"
    />
    <Transition name="fade">
      <div
        v-if="set.showBackgroundGray"
        class="background-overlay"
        :style="{ backgroundImage: `url(${backgroundImage})` }"
      />
    </Transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from "vue";
import { statusStore, setStore } from "@/stores";

const set = setStore();
const status = statusStore();
const bgUrl = ref(null);
const imgTimeout = ref(null);
const emit = defineEmits(["loadComplete"]);

// 计算背景图 URL
const backgroundImage = computed(() => set.backgroundImageUrl || "https://images.zxl.cc.ua/blog/12.webp");

// 生成随机背景图编号（请根据图片数量修改 `3`）
const bgRandom = Math.floor(Math.random() * 3 + 1);

// 设置背景图片
const setBgUrl = () => {
  const { backgroundType, backgroundCustom } = set;
  switch (backgroundType) {
    case 0:
      bgUrl.value = `/background/bg${bgRandom}.jpg`;
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
      bgUrl.value = backgroundCustom || "/background/bg1.jpg"; // 预防空值
      break;
    default:
      bgUrl.value = `/background/bg${bgRandom}.jpg`;
      break;
  }
};

// 图片加载完成
const imgLoadComplete = () => {
  imgTimeout.value = setTimeout(
    () => {
      status.setImgLoadStatus(true);
    },
    Math.floor(Math.random() * (600 - 300 + 1)) + 300
  );
};

// 图片加载失败
const imgLoadError = () => {
  console.error("壁纸加载失败：", bgUrl.value);
  $message.error("壁纸加载失败，已切换至默认图片");
  bgUrl.value = `/background/bg${bgRandom}.jpg`;
};

// 图片动画完成
const imgAnimationEnd = () => {
  console.log("壁纸加载且动画完成");
  emit("loadComplete");
};

onMounted(setBgUrl);
onBeforeUnmount(() => clearTimeout(imgTimeout.value));
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

  .background-overlay {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    opacity: 0.5; // 透明度调整
    transition: opacity 0.3s ease-in-out;
  }
}
</style>
