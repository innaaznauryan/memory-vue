<template>
  <transition name="fade" mode="out-in">
    <div class="header" :key="lives">
      <div v-if="!play">
        <p>Click Play to start.</p>
        <p>Memorize cards in 5 seconds!</p>
      </div>
      <button v-if="!play" @click="handlePlay">Play</button>
      <span v-if="play" class="lives">Lives left: {{ lives }}</span>
    </div>
  </transition>
  <div class="board" v-grid-template>
    <Card
        v-for="(card, index) in shuffled"
        :key="index"
        :index="index"
        :image="card.image"
        :shuffled="shuffled"
        :gameRun="gameRun"
        :isShown="isShown"
        @click="gameRun && handleClick(index)"
    />
  </div>
  <div class="gameOver">
    <transition name="fade">
      <span v-if="gameOver">
        {{ winner ? 'Congratulations! You win!' : 'Sorry, you lost :(' }}
      </span>
    </transition>
  </div>
</template>

<script setup>
import {onMounted, ref, watch} from "vue"
import Card from './Card.vue'
import img1 from "/src/assets/images/1.jpg"
import img2 from "/src/assets/images/2.jpg"
import img3 from "/src/assets/images/3.jpg"
import img4 from "/src/assets/images/4.jpg"
import img5 from "/src/assets/images/5.jpg"
import img6 from "/src/assets/images/6.jpg"
import img7 from "/src/assets/images/7.jpg"
import img8 from "/src/assets/images/8.jpg"
import congratsSound from "/src/assets/audio/congrats.mp3"
import failSound from "/src/assets/audio/fail.mp3"
import loseSound from "/src/assets/audio/lose.mp3"
import successSound from "/src/assets/audio/success.mp3"

const cards = [{image: img1}, {image: img2}, {image: img3}, {image: img4}, {image: img5}, {image: img6}, {image: img7}, {image: img8}]

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
  if (shuffled.value[index].flipped) {
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

const success = () => {
  flippedIndexes.value = []
  const sound = new Audio(successSound)
  sound.play()
  successCount.value++
}

const fail = () => {
  shuffled.value[flippedIndexes.value[0]].flipped = false
  shuffled.value[flippedIndexes.value[1]].flipped = false
  flippedIndexes.value = []
  lives.value--
  const sound = new Audio(failSound)
  sound.play()
}

const win = () => {
  winner.value = true
  const sound = new Audio(congratsSound)
  sound.play()
  resetGame()
}

const lose = () => {
  winner.value = false
  const sound = new Audio(loseSound)
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
.header {
  max-width: 80vmin;
  height: 50px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
  margin: 20px auto;

  p {
    color: #382d7c;
    font-weight: 600;
    padding: 2px;
    font-size: 14px;
    @media (max-width: 768px) {
      font-size: 12px;
    }
  }
}

.board {
  display: grid;
  gap: 5px;
  max-width: 80vmin;
  margin: 0 auto;
}

button {
  padding: 10px;
  background-color: #382d7c;
  color: #ddd;
  border: 0;
  border-radius: 5px;
  cursor: pointer;
  font-weight: 600;
  font-size: 20px;
  @media (max-width: 768px) {
    font-size: 14px;
  }
}

.lives {
  color: #382d7c;
  font-weight: 600;
  font-size: 20px;
  white-space: nowrap;
  @media (max-width: 768px) {
    font-size: 14px;
  }
}

.gameOver {
  text-align: center;
  height: 30px;
  color: #382d7c;
  font-weight: 600;
  font-size: 28px;
  padding: 20px;
  @media (max-width: 768px) {
    font-size: 20px;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>