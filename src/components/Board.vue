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
import {onMounted, computed, ref} from "vue"
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

const duplicated = cards.concat(cards)
const shuffled = ref([])

const play = ref(false)

function shuffle () {
  shuffled.value = [...duplicated]
  for (let i = duplicated.length - 1; i >= 0; i--) {
    const randomIndex = Math.round(Math.random() * i);
    [shuffled.value[i], shuffled.value[randomIndex]] = [shuffled.value[randomIndex], shuffled.value[i]]
  }
}

const gameRun = ref(false)

const isShown = ref(false)

const handlePlay = () => {
  play.value = true
  isShown.value = true
  setTimeout(() => {
    isShown.value = false
    gameRun.value = true
  }, 5000);
}

const currentSelection = ref(null)
const previousSelection = ref(null)

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

const isVisible = (card, index) => {
  return isShown.value || shuffled.value[index].matched || shuffled.value[index].flipped
      // || succeeds.value.includes(card.image)
}

const flipCard = (index) => {
  if (shuffled.value[index].flipped || shuffled.value[index].matched) {
    return
  }
  shuffled.value[index].flipped = true
  if (!currentSelection.value) {
    currentSelection.value = index
  } else {
    previousSelection.value = currentSelection.value
    currentSelection.value = index

    if (shuffled.value[previousSelection.value].image === shuffled.value[currentSelection.value].image) {
      shuffled.value[previousSelection.value].matched = true
      shuffled.value[currentSelection.value].matched = true
      // checkWinner()
    } else {
      shuffled.value[previousSelection.value].flipped = false
      shuffled.value[currentSelection.value].flipped = false
    }
  }
}

onMounted(() => {
  shuffle()
  console.log(shuffled.value)
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