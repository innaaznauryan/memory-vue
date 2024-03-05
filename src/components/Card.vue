<template>
  <div
      class="card-container"
      :style="{'background-image': background}"
  >
    <div class="front-card"></div>
    <div class="back-card"></div>
  </div>
</template>

<script setup>
import {computed} from "vue"

const props = defineProps(["image", "isShown", "gameRun", "uid"])

const background = computed(() => {
  return props.isShown ? `url(src/assets/images/${props.image})` : null
})

</script>

<style scoped>
.card-container {
  aspect-ratio: 1;
  background-position: center;
  background-size: cover;
  background-color: #eee2dc;
}
</style>