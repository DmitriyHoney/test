<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, reactive, ref } from 'vue';

const isCropInit = ref(false);
const coords = reactive({
  startX: 0,
  startY: 0,

  endX: 0,
  endY: 0,

  actX: 0,
  actY: 0,
})

const elCropContainer = ref<HTMLDivElement | null>(null);

const initMouseDown = async (e: MouseEvent) => {
  removeAllEventListeners();
  await new Promise((resolve) => setTimeout(resolve, 50))
  isCropInit.value = true;
  const { offsetX, offsetY } = e;
  coords.startX = offsetX;
  coords.startY = offsetY;
  elCropContainer.value?.addEventListener('mousemove', initMouseMove)
};
const initMouseUp = (e: MouseEvent) => {
  const { pageX, pageY } = e;
  coords.endX = pageX;
  coords.endY = pageY;
  elCropContainer.value?.removeEventListener('mousemove', initMouseMove)
};
const initMouseMove = (e: MouseEvent) => {
  const { pageX, pageY } = e;
  coords.actX = pageX;
  coords.actY = pageY;
};

const handleInitCrop = () => {
  if (!elCropContainer.value) {
    console.warn('elCropContainer not found');
    return;
  }
  elCropContainer.value?.addEventListener('mousedown', initMouseDown)
  elCropContainer.value?.addEventListener('mouseup', initMouseUp)
};


const handleCropImage = () => {
  console.log('coords', coords);
  return coords;
};

const styleCrop = computed(() => {
  let offsetLeft = 0;
  let offsetTop = 0;
  if (elCropContainer?.value) {
    offsetLeft = elCropContainer?.value.offsetLeft;
    offsetTop = elCropContainer?.value.offsetTop;
  }
  
  return `
    top: ${coords.startY}px; 
    left: ${coords.startX}px;
    width: ${coords.actX - coords.startX - offsetLeft}px;
    height: ${coords.actY - coords.startY - offsetTop}px;
  `
})

const removeAllEventListeners = () => {
  coords.startX = 0;
  coords.startY = 0;

  coords.endX = 0;
  coords.endY = 0;

  coords.actX = 0;
  coords.actY = 0;

  isCropInit.value = false;
}

onMounted(() => {
  handleInitCrop();
})

onBeforeUnmount(() => {
  elCropContainer.value?.removeEventListener('mousedown', initMouseDown);
  elCropContainer.value?.removeEventListener('mousemove', initMouseMove);
  elCropContainer.value?.removeEventListener('mouseup', initMouseUp);
})
</script>

<template>
  <div class="crop">
    <div class="crop-container" ref="elCropContainer">
      <img src="https://avatars.mds.yandex.net/i?id=a54d78e4071da7d18f0f4635282afd71_l-12634740-images-thumbs&n=13" alt="фото">
      <div class="crop-container__border" ref="elCropBorder" :style="styleCrop"></div>
    </div>
    <div class="crop-actions">
      <input type="file" accept="*/image" hidden>
      <button class="base-btn base-btn__upload">Загрузить</button>
      <button class="base-btn base-btn__crop" @click="handleCropImage">Обрезать</button>
      <button class="base-btn base-btn__clear" @click="removeAllEventListeners">Очитстить</button>
    </div>
  </div>
</template>


<style scoped lang="scss">
.base-btn {
  outline: none;
  border: none;
  cursor: pointer;
  padding: 12px 20px;
  border-radius: 6px;
  &__upload {
    background-color: #affab5;
  }
  &__crop {
    margin-left: auto;
    background-color: #a4e7fc;
  }
  &__clear {
    background-color: #fca4a4;
  }
}
.crop {
  &-container {
    width: 600px;
    height: 400px;
    border: 1px solid black;
    border-radius: 12px;
    user-select: none;
    position: relative;
    & img {
      width: 100%;
      height: 100%;
      object-fit: contain;
      pointer-events: none;
    }
    &__border {
      position: absolute;
      top: 0;
      left: 0;
      width: 0px;
      height: 0px;
      border: 1px solid red;
    }
  }
  
  &-actions {
    margin-top: 20px;
    display: flex;
    align-items: center;
    gap: 4px;
  }
}
</style>