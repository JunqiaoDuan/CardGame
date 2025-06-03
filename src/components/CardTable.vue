<script setup lang="ts">
import CardPlayer from './CardPlayer.vue'
import StartPlay from './StartPlay.vue'
import WinnerDisplay from './WinnerDisplay.vue'
import { ref } from 'vue'

interface Player {
  nickname: string
  avatar: string
  cards: Card[]
}

// Card interface
interface Card {
  value: string  // 2-10, J, Q, K, A
  suit: string   // @, #, ^, *
  isAnimating?: boolean  // Animation state flag
  isWinning?: boolean   // Winning card flag
}

interface WinnerInfo {
  index: number
  nickname: string
  reason: string
  score: number
  highestCard: Card
}

const winner = ref<WinnerInfo | null>(null)

// Generate card array
const generateCards = (): Card[] => {
  const values = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A']
  const suits = ['@', '#', '^', '*']
  const cards: Card[] = []

  for (const value of values) {
    for (const suit of suits) {
      cards.push({ value, suit })
    }
  }
  return cards
}

// Shuffle cards function
const shuffleCards = (cards: Card[]): Card[] => {
  const shuffled = [...cards]
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]]
  }
  return shuffled
}

// Generate and shuffle cards
const cards = shuffleCards(generateCards())

const getRandomString = () => Math.random().toString(36).substring(7)

const players = ref<Player[]>(Array.from({ length: 4 }, (_, index) => ({
  nickname: `Player ${index + 1}`,
  avatar: `https://api.multiavatar.com/${getRandomString()}.svg`,
  cards: []
})))

// Game state
const isGameStarted = ref(false)
const isDealing = ref(false)
const showStartButton = ref(true)  // Control start button visibility
const gameCards = ref<Card[]>([])

// Initialize with 52 cards
const remainingCards = ref(52)

// Calculate player's score
const calculatePlayerScore = (player: Player): { 
  score: number,      // Maximum count of same value cards
  value: string,      // Card value with maximum count
  maxSuit: string     // Highest suit among cards with maximum count
} => {
  const cardCount: Record<string, number> = {}
  const suitOrder = { '*': 4, '^': 3, '#': 2, '@': 1 }
  const valueGroups: Record<string, string[]> = {}  // Store suits for each card value

  // Count occurrences of each value and their suits
  player.cards.forEach(card => {
    const key = card.value
    cardCount[key] = (cardCount[key] || 0) + 1
    if (!valueGroups[key]) valueGroups[key] = []
    valueGroups[key].push(card.suit)
  })

  // Find values with highest count
  const maxCount = Math.max(...Object.values(cardCount))
  const maxCards = Object.keys(cardCount).filter(key => cardCount[key] === maxCount)

  // Find highest value among cards with same count
  const maxValue = maxCards.reduce((prev, curr) => (curr > prev ? curr : prev), '')

  // Find highest suit among cards with maxValue
  const suits = valueGroups[maxValue]
  const maxSuit = suits.reduce((prev, curr) => 
    suitOrder[curr as keyof typeof suitOrder] > suitOrder[prev as keyof typeof suitOrder] ? curr : prev
  )

  return { score: maxCount, value: maxValue, maxSuit }
}

// Determine winner
const determineWinner = () => {
  let winnerIndex = -1
  let highestScore = 0
  let highestValue = ''
  let highestSuit = ''

  players.value.forEach((player, index) => {
    const { score, value, maxSuit } = calculatePlayerScore(player)

    if (score > highestScore || 
       (score === highestScore && value > highestValue) ||
       (score === highestScore && value === highestValue && maxSuit > highestSuit)) {
      winnerIndex = index
      highestScore = score
      highestValue = value
      highestSuit = maxSuit
    }
  })

  // Mark winning cards
  players.value[winnerIndex].cards.forEach(card => {
    if (card.value === highestValue) {
      card.isWinning = true
    }
  })

  winner.value = {
    index: winnerIndex,
    nickname: players.value[winnerIndex].nickname,
    reason: '',  // Text explanation not needed
    score: highestScore,
    highestCard: { value: highestValue, suit: highestSuit }
  }
}

// Handle game start
const handleGameStart = async () => {
  console.log('handleGameStart')
  if (isDealing.value) return // Prevent multiple deals

  isGameStarted.value = true
  isDealing.value = true
  showStartButton.value = true  // Keep start button visible
  
  try {
    // 1. Generate and shuffle new cards
    gameCards.value = shuffleCards(generateCards())
    console.log('Shuffling completed', gameCards.value)
    
    // 2. Deal cards
    await dealCards()
    console.log('Dealing completed', players.value)

    // 3. Calculate winner
    determineWinner()
    
    // Hide start button after dealing is complete
    showStartButton.value = false

  } catch (error) {
    console.log('Error during game start:', error)
    isGameStarted.value = false // Reset game state on error
    showStartButton.value = true  // Show start button on error
  } finally {
    isDealing.value = false
  }
}

// Card sorting function
const sortCards = (cards: Card[]): Card[] => {
  const cardOrder = {
    'A': 14, 'K': 13, 'Q': 12, 'J': 11,
    '10': 10, '9': 9, '8': 8, '7': 7,
    '6': 6, '5': 5, '4': 4, '3': 3, '2': 2
  }
  const suitOrder = { '*': 4, '^': 3, '#': 2, '@': 1 }

  return [...cards].sort((a, b) => {
    // Sort by value first
    const valueCompare = cardOrder[a.value as keyof typeof cardOrder] - 
                        cardOrder[b.value as keyof typeof cardOrder]
    if (valueCompare !== 0) return valueCompare

    // Then sort by suit if values are equal
    return suitOrder[a.suit as keyof typeof suitOrder] - 
           suitOrder[b.suit as keyof typeof suitOrder]
  })
}

// Deal cards function
const dealCards = async () => {
  // Clear all players' hands
  players.value.forEach(player => player.cards = [])
  
  // Deal cards in turns (13 cards each)
  for (let i = 0; i < 52; i++) {
    const playerIndex = i % 4
    
    // Add card with animation
    const card = {
      ...gameCards.value[i],
      isAnimating: true
    }
    
    players.value[playerIndex].cards.push(card)
    
    // Wait for animation
    await new Promise(resolve => setTimeout(resolve, 60))
    
    // Remove animation flag
    const lastCardIndex = players.value[playerIndex].cards.length - 1
    if (players.value[playerIndex].cards[lastCardIndex]) {
      players.value[playerIndex].cards[lastCardIndex].isAnimating = false
    }

    // Sort cards after each deal
    players.value[playerIndex].cards = sortCards(players.value[playerIndex].cards)

    // Decrease remaining cards count after animation
    remainingCards.value = Math.max(0, remainingCards.value - 1)
  }
}

// Reset game state
const resetGame = () => {
  // Reset all states
  isGameStarted.value = false
  isDealing.value = false
  remainingCards.value = 52
  winner.value = null
  showStartButton.value = false  // Hide start button
  
  // Clear all players' cards and winning marks
  players.value.forEach(player => {
    player.cards = []
  })
  
  // Generate and shuffle new cards
  gameCards.value = []

  // Auto start new game after short delay
  setTimeout(() => {
    handleGameStart()
  }, 500)  // 500ms delay to show reset state
}
</script>

<template>
  <div class="card-table">
    <CardPlayer
      :nickname="players[0].nickname"
      :avatar="players[0].avatar"
      :cards="players[0].cards"
      position="top"
      :is-game-started="isGameStarted"
      :is-winner="winner?.index === 0"
    />
    <div class="middle-section">
      <CardPlayer
        :nickname="players[1].nickname"
        :avatar="players[1].avatar"
        :cards="players[1].cards"
        position="left"
        :is-game-started="isGameStarted"
        :is-winner="winner?.index === 1"
      />
      <div class="table-center">
        <StartPlay 
          v-if="showStartButton"
          @start="handleGameStart" 
          :disabled="isDealing"
          :is-game-started="isGameStarted"
          :remaining-cards="remainingCards"
        />
        <WinnerDisplay
          v-if="winner"
          :winner-nickname="winner.nickname"
          :reason="winner.reason"
          :show="isGameStarted && !isDealing"
          @play-again="resetGame"
        />
      </div>
      <CardPlayer
        :nickname="players[2].nickname"
        :avatar="players[2].avatar"
        :cards="players[2].cards"
        position="right"
        :is-game-started="isGameStarted"
        :is-winner="winner?.index === 2"
      />
    </div>
    <CardPlayer
      :nickname="players[3].nickname"
      :avatar="players[3].avatar"
      :cards="players[3].cards"
      position="bottom"
      :is-game-started="isGameStarted"
      :is-winner="winner?.index === 3"
    />
  </div>
</template>

<style scoped>
.card-table {
  width: 600px;
  height: 600px;
  background-color: #5dc05d;
  border-radius: 8px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}

.middle-section {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  height: 400px;
  position: relative;
}

.table-center {
  width: 360px;
  height: 360px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  background-color: #57a157;
  border-radius: 4px;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: visible;
}
</style>