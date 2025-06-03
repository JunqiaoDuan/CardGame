<script setup lang="ts">
import DiceAnimation from './DiceAnimation.vue'

interface Props {
  winnerNickname: string
  reason: string
  show: boolean
}

defineProps<Props>()
const emit = defineEmits(['playAgain'])
</script>

<template>
  <div v-if="show" class="winner-display">
    <div class="fireworks"></div>
    <div class="winner-info">
      <h5>🎉 {{ winnerNickname }} Wins!</h5>
      <button class="play-again-btn" @click="emit('playAgain')">Play Again</button>
    </div>
  </div>
</template>

<style scoped>
.winner-display {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 100;
  text-align: center;
  background: rgba(255, 255, 255, 0.98);
  padding: 25px 35px;
  border-radius: 12px;
  box-shadow:
    0 10px 30px rgba(0, 0, 0, 0.1),
    0 0 0 1px rgba(0, 0, 0, 0.05);
  backdrop-filter: blur(10px);
}

.winner-info h5 {
  color: #333333;
  font-size: 14px;
  margin-bottom: 20px;
  font-weight: 600;
}

.play-again-btn {
  margin-top: 20px;
  padding: 10px 24px;
  background: linear-gradient(145deg, #4caf50, #43a047);
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 12px;
  font-weight: 500;
  letter-spacing: 0.5px;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(76, 175, 80, 0.3);
}

.play-again-btn:hover {
  background: linear-gradient(145deg, #43a047, #388e3c);
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(76, 175, 80, 0.4);
}

.play-again-btn:active {
  transform: translateY(1px);
}

.fireworks {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  animation: none;
}

h4 {
  font-size: 24px;
  margin-bottom: 10px;
  color: #2c3e50;
}

.winning {
  background: linear-gradient(145deg, #ffd700, #ffc107);
  box-shadow:
    0 0 20px rgba(255, 215, 0, 0.6),
    0 0 40px rgba(255, 215, 0, 0.3);
  border: 2px solid rgba(255, 215, 0, 0.5);
  animation: glow 1s ease-in-out infinite alternate;
}

@keyframes glow {
  from {
    box-shadow:
      0 0 5px #fff,
      0 0 10px #fff,
      0 0 15px #ffd700;
  }
  to {
    box-shadow:
      0 0 10px #fff,
      0 0 20px #fff,
      0 0 30px #ffd700;
  }
}
</style>
