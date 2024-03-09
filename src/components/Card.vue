<template>
  <div
    class="card-container"
    :style="{'cursor': gameRun ? 'pointer' : 'default'}"
    @click="handleClick">
      <div 
        class="front-card"
        :style="{'background-image': `url(src/assets/images/${image})`, 'transform': isShown ? 'rotateY(0)' : 'rotateY(180deg)'}">
      </div>
      <div 
        class="back-card" 
        :style="{'transform': isShown ? 'rotateY(-180deg)' : 'rotateY(0)'}">
      </div>
  </div>
</template>

<script setup>
defineProps(["image", "gameRun", "isShown"])
</script>

<style scoped>
.card-container {
  aspect-ratio: 1;
  position: relative;
  perspective: 500px;
}

.card-container * {
  position: absolute;
  width: 100%;
  height: 100%;
  transition: .5s;
  backface-visibility: hidden;
}

.front-card {
  transform: rotateY(180deg);
  background-position: center;
  background-size: cover;
}

.back-card {
  transform: rotateY(0);
  background-color: #d9b08c;
}
</style>