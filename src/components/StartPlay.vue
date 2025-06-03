<template>
  <div class="game-center">
    <button @click="emit('start')" :disabled="disabled" class="start-button" v-if="!isGameStarted">
      Start Game
    </button>
    <div class="deck-area">
      <div class="card-stack">
        <div
          class="stacked-card"
          v-for="i in Math.min(5, remainingCards)"
          :key="i"
          :style="{ transform: `translateY(${(i - 1) * -1}px)` }"
          v-show="remainingCards > 0"
        >
          <div class="card-back">
            <div class="pattern"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
export default { name: 'StartPlay' }
</script>

<script setup lang="ts">
const props = withDefaults(
  defineProps<{
    disabled?: boolean
    isGameStarted?: boolean
    remainingCards?: number
  }>(),
  {
    remainingCards: 52,
    isGameStarted: false,
    disabled: false,
  },
)

const emit = defineEmits<{
  (e: 'start'): void
}>()
</script>

<style scoped>
.game-center {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 30px;
}

.start-button {
  padding: 12px 24px;
  font-size: 16px;
  border-radius: 4px;
  border: none;
  background-color: #4caf50;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

.start-button:hover:not(:disabled) {
  background-color: #45a049;
}

.start-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.deck-area {
  position: relative;
  width: 100%;
  display: flex;
  justify-content: center;
}

.card-stack {
  position: relative;
  width: 60px;
  height: 90px;
}

.stacked-card {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 8px;
  border: 1px solid #ccc;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  background: #2c3e50;
  transition: all 0.3s ease-out;
}

.card-back {
  width: 100%;
  height: 100%;
  border-radius: 8px;
}

.pattern {
  width: 100%;
  height: 100%;
  border-radius: 8px;
  background-size: 24px 24px;
  opacity: 0.6;
  background-image: linear-gradient(
    45deg,
    rgba(255, 255, 255, 0.1) 25%,
    transparent 25%,
    transparent 50%,
    rgba(255, 255, 255, 0.1) 50%,
    rgba(255, 255, 255, 0.1) 75%,
    transparent 75%
  );
}
</style>
