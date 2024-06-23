<script setup lang="ts">
import { ref } from 'vue'
import Swiper from './components/Swiper.vue'
const imgList = ref([
  './src/assets/img/52d325827153189c76c0956f8fe39edc.jpg',
  './src/assets/img/96f4de71bc791b8c2505fd0dc7ae4546.jpg',
  './src/assets/img/590ee8d39d52bf563aa535218046cfd2.jpg',
  './src/assets/img/a191d6b4a66575c88255325f64798b24.jpg',
  './src/assets/img/b18c10c0a5f13d111e765006757ea566.jpg',
])

</script>

<template>
  <Swiper :imgList="imgList" />
</template>

<style scoped></style>
