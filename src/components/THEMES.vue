<script setup>
import { CdSymbolColor } from '@kalimahapps/vue-icons';
</script>

<template>

  <div class="themes-menu-btn" title="theme menu">
    <CdSymbolColor />
  </div>

  <div class="THEMES">
    <div>theme settings:</div>
  </div>
</template>

<style lang="css" scoped>
.themes-menu-btn {
  font-size: 2.3rem;
  cursor: pointer;
}

.THEMES {
  display: none;
}
</style>
