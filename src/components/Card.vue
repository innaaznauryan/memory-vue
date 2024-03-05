<template>
    <div 
    class="card-container" 
    :style="{'background-image': background}" 
    @click="handleClick"
    >
        <div class="front-card"></div>
        <div class="back-card" ></div>
    </div>
</template>

<script setup>
import {computed} from "vue"

const props = defineProps(["image", "isShown", "gameRun", "uid"])
const emit = defineEmits()

const background = computed(() => {
    return props.isShown ? `url(src/assets/images/${props.image})` : null
})

const handleClick = () => {
    console.log(props.isShown)
    if(props.gameRun) {
        console.log("click")
        emit("choose", {uid: props.uid, image: props.image})
    }
}
</script>

<style scoped>
.card-container {
    width: 25%;
    aspect-ratio: 1;
    background-position: center;
    background-size: cover;
    background-color: green;
}
</style>