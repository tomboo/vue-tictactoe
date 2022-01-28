<script setup>
  import { ref } from 'vue'
  import Board from './Board.vue'
  import Toggle from './Toggle.vue'

  const nextMove = ref(0)

  function resetMove() {
    nextMove.value = 0
  }
  
  function updateMove() {
    nextMove.value++
  }

</script>

<template>
  <h2>Game</h2>
  <Board />
  <Toggle />
</template>

<style scoped>
</style>
