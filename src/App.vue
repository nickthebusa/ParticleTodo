<script setup>
import TODO from './components/TODO.vue'
import BG from './components/BG.vue'
import THEMES from './components/THEMES.vue'
</script>

<template>
  <div class="top-bar">
    <BG />
    <THEMES />
  </div>
  <TODO />
</template>

<style lang="css" scoped>
.top-bar {
  width: 100%;
  display: flex;
  justify-content: flex-start;
  align-items: start;
}
</style>
