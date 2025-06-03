<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'CardPlayer',
  props: {
    nickname: {
      type: String,
      required: true
    },
    avatar: {
      type: String,
      required: true
    },
    position: {
      type: String as () => 'top' | 'bottom' | 'left' | 'right',
      required: true
    },
    cards: {
      type: Array as () => { 
        value: string
        suit: string
        isAnimating?: boolean 
        isWinning?: boolean
      }[],
      default: () => []
    },
    isGameStarted: {
      type: Boolean,
      default: false
    },
    isWinner: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    cardOffset() {
      return (this.cards.length - 1) * 7.5
    }
  }
})
</script>

<template>
  <div 
    class="player" 
    :class="[
      position,
      { 'winner-shake': isWinner }
    ]"
  >
    <div class="player-seat">
      <div v-if="isWinner" class="crown">👑</div>
      <img :src="avatar" class="avatar" :alt="`${nickname}'s avatar`">
      <div class="nickname">{{ nickname }}</div>
    </div>
    <div class="cards-container">
      <div
        v-for="(card, index) in cards"
        :key="index"
        class="card"
        :class="{ 
          'dealing': card.isAnimating,
          'winning': card.isWinning
        }"
        :style="{ 
          '--index': index,
          '--total-offset': `${cardOffset}px`
        }"
      >
        {{ card.value }}{{ card.suit }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.player {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.player-seat {
  position: relative;
  width: 60px;
  height: 75px;
  padding: 5px;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.2);
  border-radius: 4px;
  color: white;
  font-size: 12px;
}

.avatar {
  width: 45px;
  height: 45px;
  border-radius: 50%;
  object-fit: cover;
  border: 1px solid white;
}

.nickname {
  margin-top: 5px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 55px;
  line-height: 1.2;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}

.cards-container {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  min-width: 200px;
  height: 80px;
  transform-origin: center center;
}

/* 调整卡牌容器相对于玩家的位置 */
.top .cards-container {
  position: absolute;
  top: 90px;
}

.bottom .cards-container {
  position: absolute;
  bottom: 90px;
}

.left .cards-container {
  position: absolute;
  left: 40px;  /* 补偿头像移动的距离，保持卡牌位置不变 */
  top: -50px;
  height: 200px;
  width: 80px;
}

.right .cards-container {
  position: absolute;
  right: 40px;  /* 补偿头像移动的距离，保持卡牌位置不变 */
  top: -50px;
  height: 200px;
  width: 80px;
}

.card {
  position: absolute;
  background: white;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 5px;
  width: 25px;
  height: 38px;
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  font-size: 10px;
  transition: all 0.3s ease;
  padding-top: 2px;
  padding-left: 2px;
  z-index: 1;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
}

/* 让卡牌重叠排列 */
.bottom .card {
  transform: translateX(calc(var(--index) * 15px - var(--total-offset)));
}

.top .card {
  transform: translateX(calc(var(--index) * 15px - var(--total-offset)));
}

.left .card {
  transform: translateY(calc(var(--index) * 15px - var(--total-offset)));
}

.right .card {
  transform: translateY(calc(var(--index) * 15px - var(--total-offset)));
}

.dealing {
  animation: dealAnimation 0.3s ease-out;
}

@keyframes dealAnimation {
  from {
    transform: translate(-50%, -50%) scale(0.5);
    opacity: 0;
  }
  to {
    transform: translate(0, 0) scale(1);
    opacity: 1;
  }
}

/* 调整不同位置的玩家容器布局 */
.player.top {
  margin-bottom: auto;
  padding-bottom: 30px;
}

.player.bottom {
  margin-top: auto;
  padding-top: 30px;
}

.player.left {
  margin-right: auto;
  padding-right: 20px;  /* 头像向右靠拢20px */
}

.player.right {
  margin-left: auto;
  padding-left: 20px;  /* 头像向左靠拢20px */
}

.crown {
  position: absolute;
  top: -25px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 28px;
  filter: drop-shadow(0 3px 6px rgba(0, 0, 0, 0.2));
}

.winner-shake {
  animation: shake 0.5s ease-in-out;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

.winning {
  background: linear-gradient(145deg, #ffd700, #ffc107);
  box-shadow: 
    0 0 20px rgba(255, 215, 0, 0.6),
    0 0 40px rgba(255, 215, 0, 0.3);
  border: 2px solid rgba(255, 215, 0, 0.5);
  transform: scale(1.05);
}
</style>  