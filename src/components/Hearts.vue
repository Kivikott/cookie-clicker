
<template>
  <div class="hearts-container">
    <div
      v-for="heart in hearts"
      :key="heart.id"
      class="heart"
      :style="{
        left: heart.left + '%',
        animationDuration: heart.duration + 's',
        animationDelay: heart.delay + 's',
        fontSize: heart.size + 'px',
      }"
    >
      {{ heart.symbol }}
    </div>
  </div>
</template>
 
<script setup>
import { ref, onMounted } from 'vue'
 
const hearts = ref([])
const symbols = ['💗', '💖', '💕', '💘', '🌹', '💌']
 
onMounted(() => {
  // Create random floating hearts
  for (let i = 0; i < 30; i++) {
    hearts.value.push({
      id: i,
      left: Math.random() * 100,
      duration: 6 + Math.random() * 10,
      delay: Math.random() * -10, // negative delay so hearts are already mid-air on load
      size: 14 + Math.random() * 16,
      symbol: symbols[Math.floor(Math.random() * symbols.length)],
    })
  }
})
</script>
 
<style scoped>
.hearts-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none; /* so you can still click the cookie */
  overflow: hidden;
}
 
.heart {
  position: absolute;
  bottom: -40px;
  opacity: 0.85;
  animation-name: rise;
  animation-timing-function: ease-in-out;
  animation-iteration-count: infinite;
}
 
@keyframes rise {
  0% {
    transform: translate(0, 0) rotate(-8deg);
  }
  50% {
    transform: translate(18px, -55vh) rotate(8deg);
  }
  100% {
    transform: translate(-10px, -110vh) rotate(-8deg);
  }
}
 
@media (prefers-reduced-motion: reduce) {
  .heart {
    animation: none;
    bottom: auto;
    top: 20%;
  }
}
</style>
 
Claude finished the response
