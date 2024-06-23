<template>
  <div class="hw-swiper" ref="swiperContainer">
    <div class="img-box" ref="imgBoxContainer">
      <img v-for="item in imgList" :src="item">
    </div>
    <div class="swiper-l" @click="swiperByArrow(0)">
      <svg t="1719042830030" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
        p-id="4247" width="200" height="200">
        <path
          d="M672 896c8.2 0 16.4-3.1 22.6-9.4 12.5-12.5 12.5-32.8 0-45.3L365.3 512l329.4-329.4c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0l-352 352c-12.5 12.5-12.5 32.8 0 45.3l352 352c6.2 6.3 14.4 9.4 22.6 9.4z"
          fill="#1875F0" p-id="4248"></path>
      </svg>
    </div>
    <div class="swiper-r" @click="swiperByArrow(1)">
      <svg t="1719042923565" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
        p-id="3009" width="200" height="200">
        <path
          d="M352 896c-8.2 0-16.4-3.1-22.6-9.4-12.5-12.5-12.5-32.8 0-45.3L658.7 512 329.4 182.6c-12.5-12.5-12.5-32.8 0-45.3s32.8-12.5 45.3 0l352 352c12.5 12.5 12.5 32.8 0 45.3l-352 352c-6.3 6.3-14.5 9.4-22.7 9.4z"
          fill="#1875F0" p-id="3010"></path>
      </svg>
    </div>
    <div class="swiper-b" ref="pageContainer">
      <div v-for="(_, index) in imgList" class="circle" :class="{ active: index === 0 }" @click="swiperByPage(index)">
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
// 子组件
import { ref, onMounted, defineProps } from 'vue'
const props = defineProps({
  imgList: {
    type: Array<string>,
    required: true
  },
  autoPlay: {
    type: String,
    default: "true"
  }
})

const moveXList = ref<number[]>([])
const swiperContainer = ref<null | HTMLDivElement>(null)
const imgBoxContainer = ref<null | HTMLDivElement>(null)
const pageContainer = ref<null | HTMLDivElement>(null)
let swiperWidth = 0
let time: null | number = null;

onMounted(() => {
  init()
})

function init() {
  // 获取容器宽度
  if (swiperContainer.value) {
    swiperWidth = swiperContainer.value.offsetWidth
  }
  for (let i = 0; i < props.imgList.length; i++) {
    moveXList.value.push(i * swiperWidth * (-1))
  }
  moveXList.value[0] = 0
  if (props.autoPlay == 'true') {
    time = setInterval(() => {
      autoPlaySwiper()
    }, 2500)
  }
}
// 自动播放
function autoPlaySwiper() {
  let curIndex = getCurImgIndex()
  if (curIndex == -1) return
  let targetIndex = curIndex == props.imgList.length - 1 ? 0 : curIndex + 1
  let dis = moveXList.value[targetIndex]
  imgBoxContainer.value!.style.transform = `translateX(${dis}px)`;
  changePageStyle(curIndex, targetIndex)
}

// 获取当前图片位置
function getCurImgIndex(): number {
  if (!imgBoxContainer.value) return -1
  const transformValue = window.getComputedStyle(imgBoxContainer.value!).transform
  let translateX = 0
  if (transformValue !== 'none') {
    const matrix = transformValue.match(/matrix\((.+)\)/)![1].split(', ');
    translateX = parseFloat(matrix[4]); // 第5个值是tx
  }
  let curIndex = moveXList.value.indexOf(translateX)
  return curIndex
}

// 点击前进后退滚动
function swiperByArrow(type: number) {
  if (props.autoPlay == 'true' && time !== null) {
    clearInterval(time)
    time = setInterval(() => {
      autoPlaySwiper()
    }, 2500)
  }
  const lastTranslateX = swiperWidth * (props.imgList.length - 1) * (-1)
  let translateX: number = 0, dis = null
  let curIndex = getCurImgIndex()
  translateX = moveXList.value[curIndex]
  if (type) {
    translateX == lastTranslateX ? dis = 0 : dis = translateX - swiperWidth
  } else {
    translateX == 0 ? dis = lastTranslateX : dis = translateX + swiperWidth
  }
  let targetIndex = moveXList.value.indexOf(dis)
  imgBoxContainer.value!.style.transform = `translateX(${dis}px)`;
  changePageStyle(curIndex, targetIndex)
}
// 点击分页器滚动
function swiperByPage(targetIndex: number) {
  if (props.autoPlay == 'true' && time !== null) {
    clearInterval(time)
    time = setInterval(() => {
      autoPlaySwiper()
    }, 2500)
  }
  let curIndex = getCurImgIndex()
  let dis = moveXList.value[targetIndex]
  imgBoxContainer.value!.style.transform = `translateX(${dis}px)`;
  changePageStyle(curIndex, targetIndex)
}
// 改变分页器样式
function changePageStyle(curIndex: number, targetIndex: number) {
  if (curIndex == -1 || targetIndex == -1) return
  pageContainer.value!.children[curIndex].classList.remove('active');
  pageContainer.value!.children[targetIndex].classList.add('active');
}
</script>

<style lang="scss" scoped>
.hw-swiper {
  width: 800px;
  height: 400px;
  background-color: #fff;
  text-align: center;
  position: relative;
  background-size: cover;
  background-repeat: no-repeat;
  transition: opacity 0.3s;
  overflow: hidden;

  .img-box {
    width: 100%;
    height: 100%;
    display: flex;
    position: relative;
    // transform: translateX(-800px);
    transition: transform 0.5s ease-in-out;
  }

  img {
    width: 100%;
    height: 100%;
    flex-shrink: 0;
    object-fit: cover;
  }

  .swiper-l {
    width: 60px;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0.5;
    z-index: 10;

  }

  .swiper-r {
    width: 60px;
    height: 100%;
    position: absolute;
    top: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0.5;
  }

  .swiper-l:hover {
    opacity: 1;
    cursor: pointer;
  }

  .swiper-r:hover {
    opacity: 1;
    cursor: pointer;
  }

  .swiper-b {
    width: 150px;
    height: 40px;
    position: absolute;
    bottom: 0;
    left: 50%;
    z-index: 10;
    transform: translate(-50%);
    display: flex;
    justify-content: space-between;
    align-items: center;

    .circle {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      background-color: grey;
      transition: width 0.1s, height 0.1s;
    }

    .circle:hover {
      background-color: rgb(0, 122, 255);
      cursor: pointer;
    }

    .active {
      width: 15px;
      height: 15px;
      border-radius: 50%;
      background-color: rgb(0, 122, 255);
    }


  }
}
</style>