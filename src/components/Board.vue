<template>
  <button v-if="!play" @click="handlePlay">Play</button>
  <span class="lives">Lifes left: {{ lives }}</span>
  <div class="board" v-grid-template>
    <Card
      v-for="(card, index) in shuffled"
      :key="index"
      :image="card.image"
      :gameRun="gameRun"
      :isShown="isVisible(index)"
      @click="gameRun && handleClick(index)"
    />
  </div>
  <div class="gameOver">
    <span v-if="gameOver">
      {{ winner ? 'Congratulations! You win!' : 'Sorry, you lose :(' }}
    </span>
  </div>
</template>

<script setup>
import {onMounted, ref, watch} from "vue"
import Card from './Card.vue'

const cards = [{image: "1.jpg"}, {image: "2.jpg"}, {image: "3.jpg"}, {image: "4.jpg"}, {image: "5.jpg"}, {image: "6.jpg"}, {image: "7.jpg"}, {image: "8.jpg"}]

const vGridTemplate = {
  mounted: element => element.style["grid-template-columns"] = `repeat(${Math.sqrt(cards.length * 2)}, 1fr)`
}

const shuffled = ref([])
const play = ref(false)
const gameRun = ref(false)
const isShown = ref(false)
const flippedIndexes = ref([])
const successCount = ref(0)
const lives = ref(Math.floor(cards.length * 70 / 100))
const gameOver = ref(false)
const winner = ref(false)

const getCards = () => {
  const cardsCopy = cards.map(card => ({...card}))
  const duplicated = [...cards, ...cardsCopy]
  shuffled.value = duplicated.map(card => ({...card, flipped: false}))
}

const shuffle = () => {
  for (let i = shuffled.value.length - 1; i >= 0; i--) {
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
  }, 5000)
}

const resetGame = () => {
  gameOver.value = true
  gameRun.value = false
  setTimeout(() => {
    play.value = false
    gameOver.value = false
    winner.value = false
    lives.value = Math.floor(cards.length * 70 / 100)
    successCount.value = 0
    getCards()
    shuffle()
  }, 5000)
}

const handleClick = (index) => {
  if(shuffled.value[index].flipped) {
    return
  }
  if (!flippedIndexes.value.length) {
    flippedIndexes.value.push(index)
    shuffled.value[index].flipped = true
  } else if (flippedIndexes.value.length === 1) {
    flippedIndexes.value.push(index)
    shuffled.value[index].flipped = true
    if (shuffled.value[flippedIndexes.value[0]].image === shuffled.value[flippedIndexes.value[1]].image) {
      success()
    } else {
      setTimeout(() => {
        fail()
      }, 1000)
    }
  }
}

const isVisible = (index) => {
  return isShown.value || shuffled.value[index].flipped
}

const success = () => {
  flippedIndexes.value = []
  const sound = new Audio('/src/assets/audio/success.mp3')
  sound.play()
  successCount.value++
}

const fail = () => {
  shuffled.value[flippedIndexes.value[0]].flipped = false
  shuffled.value[flippedIndexes.value[1]].flipped = false
  flippedIndexes.value = []
  lives.value--
  const sound = new Audio('/src/assets/audio/fail.mp3')
  sound.play()
}

const win = () => {
  winner.value = true
  const sound = new Audio('/src/assets/audio/congrats.mp3')
  sound.play()
  resetGame()
}

const lose = () => {
  winner.value = false
  const sound = new Audio('/src/assets/audio/lose.mp3')
  sound.play()
  resetGame()
}

watch([lives, successCount], () => {
  if (lives.value <= 0) {
    lose()
  }
  if (lives.value > 0 && successCount.value === cards.length) {
    win()
  }
})

onMounted(() => {
  getCards()
  shuffle()
})
</script>

<style scoped>
.board {
  display: grid;
  gap: 5px;
  max-width: 80vmin;
  margin: 10px auto;
  @media (max-width: 768px) {
    margin: 70px auto 10px;
  }
}

button {
  position: absolute;
  top: 0;
  padding: 10px;
  margin: 10px 20px;
  background-color: #d9b08c;
  color: #116466;
  border: 0;
  border-radius: 5px;
  cursor: pointer;
  font-weight: 600;
  font-size: 20px;
}

.lives {
  position: absolute;
  right: 0;
  top: 0;
  margin: 10px 20px;
  color: #116466;
  font-weight: 600;
}

.gameOver {
  text-align: center;
  height: 30px;
  color: #116466;
  font-weight: 600;
}
</style>