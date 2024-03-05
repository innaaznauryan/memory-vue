<template>
  <button v-if="!play" @click="handlePlay">Play</button>
  <div class="board">
    <Card
        v-for="(card, index) in shuffled"
        :key="index"
        :gameRun
        :isShown="isVisible(card, index)"
        :image="card.image"
        @click="gameRun && flipCard(index)"
    />
  </div>
</template>

<script setup>
import {onMounted, ref} from "vue"
import Card from './Card.vue'

const cards = [
  {image: "1.jpg", flipped: false, matched: false},
  {image: "2.jpg", flipped: false, matched: false},
  {image: "3.jpg", flipped: false, matched: false},
  {image: "4.jpg", flipped: false, matched: false},
  {image: "5.jpg", flipped: false, matched: false},
  {image: "6.jpg", flipped: false, matched: false},
  {image: "7.jpg", flipped: false, matched: false},
  {image: "8.jpg", flipped: false, matched: false}
]
const deepCopy = cards.map(card => ({...card}))
const duplicated = cards.concat(deepCopy)

const shuffled = ref([])
const currentIndex = ref(null)
const previousIndex = ref(null)

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
  }, 5000);
}

const success = () => {

}

const fail = () => {

}

const isVisible = (card, index) => {
  return isShown.value || shuffled.value[index]?.matched || shuffled.value[index]?.flipped
      // || succeeds.value.includes(card.image)
}

const flipCard = (index) => {
  if (shuffled.value[index].flipped || shuffled.value[index].matched) {
    return
  }
  if (currentIndex.value === null) {
    currentIndex.value = index
    shuffled.value[index].flipped = true
  } else {
    previousIndex.value = currentIndex.value
    currentIndex.value = index
    if (shuffled.value[previousIndex.value].image === shuffled.value[currentIndex.value].image) {
      shuffled.value[previousIndex.value].matched = true
      shuffled.value[currentIndex.value].matched = true
      // checkWinner()
    } else {
      shuffled.value[previousIndex.value].flipped = false
      shuffled.value[currentIndex.value].flipped = false
    }
    previousIndex.value = null
    currentIndex.value = null
    console.log(shuffled.value)
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