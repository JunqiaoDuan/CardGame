<script lang="ts">
export default { name: 'Card' }
</script>

<script setup lang="ts">
interface Props {
  value?: string
  suit?: string
  isFaceUp: boolean
}

defineProps<Props>()
</script>

<template>
  <div class="card" :class="{ 'face-up': isFaceUp }">
    <div class="card-inner">
      <div class="card-front">
        <div class="card-content">
          <span class="card-value">{{ value }}</span>
          <span class="card-suit">{{ suit }}</span>
        </div>
      </div>
      <div class="card-back">
        <div class="pattern"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  width: 30px;
  height: 45px;
  perspective: 1000px;
  margin: 2px;
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.6s;
  transform-style: preserve-3d;
  transform: rotateY(180deg);
}

.card.face-up .card-inner {
  transform: rotateY(0deg);
}

.card-front,
.card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border-radius: 4px;
  border: 1px solid #ccc;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

.card-front {
  background: white;
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  color: #000;
  padding: 2px;
}

.card-content {
  display: flex;
  align-items: center;
  gap: 1px;
}

.card-value {
  font-size: 8px;
  font-weight: bold;
}

.card-suit {
  font-size: 8px;
}

.card-back {
  background: #2c3e50;
  transform: rotateY(180deg);
}

.pattern {
  width: 100%;
  height: 100%;
  background-image: repeating-linear-gradient(
    45deg,
    #34495e 0px,
    #34495e 10px,
    #2c3e50 10px,
    #2c3e50 20px
  );
}
</style>
