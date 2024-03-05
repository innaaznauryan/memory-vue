<template>
    <button v-if="!play" @click="handlePlay">Play</button>
    <div class="board">
        <div class="row" v-for="(row, index) in layout" :key="index">
            <Card
            v-for="(card, cardIndex) in row"
            :key="cardIndex"
            :gameRun 
            :isShown = "isVisible(card, index + '' + cardIndex)"
            :image="card"
            :uid="index + '' + cardIndex"
            @choose="check"
            />
        </div>
    </div>
</template>

<script setup>
import {onMounted, computed, ref} from "vue"
import Card from './Card.vue'

const cards = ["1.jpg", "2.jpg", "3.jpg", "4.jpg", "5.jpg", "6.jpg", "7.jpg", "8.jpg"]

const duplicated = computed(() => {
    return cards.concat(cards)
})
const shuffled = ref([])

const layout = computed(() => {
    let result = []
    shuffled.value.forEach((element, index) => {
        !index || !(index % 4) ? result.push([element]) : result[result.length - 1].push(element)
    })
    return result
})

const play = ref(false)

function shuffle() {
    shuffled.value = duplicated.value
    let currentIndex = shuffled.value.length,  randomIndex;

  // While there remain elements to shuffle.
  while (currentIndex > 0) {

    // Pick a remaining element.
    randomIndex = Math.floor(Math.random() * currentIndex);
    currentIndex--;

    // And swap it with the current element.
    [shuffled.value[currentIndex], shuffled.value[randomIndex]] = [
    shuffled.value[randomIndex], shuffled.value[currentIndex]];
}

  return shuffled.value;
}

function reset () {
    shuffle()
}

function show () {
    isShown.value = true
    console.log(isShown.value)
    setTimeout(() => {
        isShown.value = false
        gameRun.value = true
    }, 5000);
}

const gameRun = ref(false)

const isShown = ref(false)

const handlePlay = () => {
    play.value = true
    show()
}

const checkImageA = ref(null)
const checkImageB = ref(null)

const succeeds = ref([])

const success = () => {
    succeeds.value.push(checkImageA.value.image)
    checkImageA.value = null
    checkImageB.value = null
}

const fail = () => {
    alert("Failed!")
    checkImageA.value = null
    checkImageB.value = null
}

const isVisible = (image, uid) => {
    return isShown.value ||
    (checkImageA.value && uid === checkImageA.value.uid && !checkImageB.value) || 
    (checkImageB.value && uid === checkImageB.value.uid && checkImageA.value.image !== checkImageB.value.image) ||
    succeeds.value.includes(image)
}

const check = (obj) => {
    checkImageA.value ? checkImageB.value = obj : checkImageA.value = obj
    if (checkImageA.value && checkImageB.value) {
        checkImageA.value.image === checkImageB.value.image ? success() : fail()
    }
}

onMounted(() => {
    reset()
})
</script>

<style scoped>
.board {
    max-width: 500px;
    margin: 0 auto;
}
.row {
    display: flex;
    gap: 5px;
    margin-bottom: 5px;
}
button {
    position: absolute;
}
</style>