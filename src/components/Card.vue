<template>
    <div
            class="card-container"
            :style="{'cursor': gameRun ? 'pointer' : 'default'}">
        <div
                class="front-card"
                :style="{'transform': isVisible ? 'rotateY(-180deg)' : 'rotateY(0)'}">
        </div>
        <div
                class="back-card"
                :style="{'background-image': `url(${image})`, 'transform': isVisible ? 'rotateY(0)' : 'rotateY(180deg)'}">
        </div>
    </div>
</template>

<script setup>
import {computed} from "vue"

const props = defineProps(["index", "image", "shuffled", "gameRun", "isShown"])
const isVisible = computed(() => {
    return props.isShown || props.shuffled[props.index].flipped
})
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
    background: linear-gradient(#382d7c, #444444);
}

.back-card {
    background-position: center;
    background-size: cover;
}
</style>