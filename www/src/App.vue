<script setup lang="ts">
import { Universe } from "game-of-life-rs";
import { ref } from 'vue'

const universe = Universe.new()
const gameOfLife = ref('loading life...')

gameOfLife.value = universe.render()

function tick () {
  universe.tick()
  gameOfLife.value = universe.render()
}

setInterval(tick, 750)
</script>

<template>
  <div id="game-of-life">
    <pre>{{ gameOfLife }}</pre>
  </div>
</template>

<style scoped>
 #game-of-life{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%; 
  height: 100%;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  gap: 4rem;
}

pre {
  font-size: 8px;
}
</style>

