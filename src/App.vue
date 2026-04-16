<script setup lang="ts">
import CropImage from './components/CropImage.vue'
</script>

<template>
  <main>
    <CropImage />
  </main>
</template>

<style lang="scss">
body {
  overflow: hidden;
}
</style>

<style scoped lang="scss">

main {
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>