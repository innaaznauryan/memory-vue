<template>
  <button v-if="!play" @click="handlePlay">Play</button>
  <div class="board">
    <Card
        v-for="(card, index) in shuffled"
        :key="index"
        :gameRun
        :isShown="isVisible(card, index)"
        :image="card.image"
        @click="gameRun && handleClick(index)"
    />
  </div>
</template>

<script setup>
import {onMounted, ref} from "vue"
import Card from './Card.vue'

const cards = [
  {image: "1.jpg", flipped: false},
  {image: "2.jpg", flipped: false},
  {image: "3.jpg", flipped: false},
  {image: "4.jpg", flipped: false},
  {image: "5.jpg", flipped: false},
  {image: "6.jpg", flipped: false},
  {image: "7.jpg", flipped: false},
  {image: "8.jpg", flipped: false}
]
const deepCopy = cards.map(card => ({...card}))
const duplicated = cards.concat(deepCopy)

const shuffled = ref([])

const play = ref(false)
const gameRun = ref(false)
const isShown = ref(false)
const succeeds = ref([])

const shuffle = () => {
  shuffled.value = duplicated
  for (let i = duplicated.length - 1; i >= 0; i--) {
    const randomIndex = Math.round(Math.random() * i);
    [shuffled.value[i], shuffled.value[randomIndex]] = [shuffled.value[randomIndex], shuffled.value[i]]
  }
}

const handlePlay = () => {
  play.value = true
  isShown.value = true
  setTimeout(() => {
    isShown.value = false
    gameRun.value = true
  }, 1000);
}

const success = () => {

}

const fail = () => {

}
const flippedIndexes = ref([])

const isVisible = (card, index) => {
  return isShown.value || shuffled.value[index].flipped
      // || succeeds.value.includes(card.image)
}

function handleClick (index) {
  if (!flippedIndexes.value.length) {
    flippedIndexes.value.push(index)
    shuffled.value[index].flipped = true
  } else if (flippedIndexes.value.length === 1) {
    flippedIndexes.value.push(index)
    shuffled.value[index].flipped = true
    if (shuffled.value[flippedIndexes.value[0]].image === shuffled.value[flippedIndexes.value[1]].image) {
      flippedIndexes.value = []
      const sound = new Audio('src/assets/audio/success.mp3')
      sound.play()
      // checkWinner()
    } else {
      setTimeout(() => {
        shuffled.value[flippedIndexes.value[0]].flipped = false
        shuffled.value[flippedIndexes.value[1]].flipped = false
        flippedIndexes.value = []
        const sound = new Audio('src/assets/audio/fail.mp3')
        sound.play()
      }, 1000)
    }
  }
}

onMounted(() => {
  shuffle()
})
</script>

<style scoped>
.board {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 5px;
  max-width: 500px;
  margin: 0 auto;
}

button {
  position: absolute;
  padding: 10px;
  margin: 10px;
  background-color: #eee2dc;
  color: #ac3b61;
  border: 0;
  border-radius: 5px;
  cursor: pointer;
  font-weight: 600;
}
</style>