<template>
  <div class="page-container">
    <div class="banner">
      <div class="circle">
        <Adventurer :size="'100%'" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { Adventurer } from 'vue-adventurer'
</script>

<style lang="less" scoped>
@import '@/styles/spacing.less';
@import '@/styles/colors.less';

@banner-height: 150px;
@banner-width: 100%;

.page-container {
  height: 100vh;

  @media (max-width: @breakpoint-m) {
    width: 100vh;
  }

  @media (min-width: @breakpoint-m) {
    width: 60vw;
    max-width: 1200px;
  }
}

.banner {
  position: relative;
  width: 100%;
  height: @banner-height;
  background-color: @gray-1;
  justify-content: center;
  display: flex;

  .circle {
    background-color: @white;
    width: 50%;
    aspect-ratio: 1;
    position: absolute;
    border-radius: 50%;
  }
}
</style>
