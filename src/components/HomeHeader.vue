<script setup lang="ts">
</script>

<template>
  <header>
    <h1 class="game-title">
      🎲 Happy Poker 🎮
    </h1>
  </header>
</template>

<style scoped>
header {
  width: 600px;
  padding: 10px;
  background-color: #2c3e50;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
}

.game-title {
  color: white;
  font-size: 2em;
  text-shadow: 
    3px 3px 0 #45a049,
    -1px -1px 0 #45a049,
    1px -1px 0 #45a049,
    -1px 1px 0 #45a049,
    1px 1px 0 #45a049;
  font-family: "Comic Sans MS", "Comic Sans", cursive;
  letter-spacing: 2px;
  animation: bounce 2s ease-in-out infinite;
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}
</style>