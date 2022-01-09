<script setup>
  defineProps({
    index: Number
  })

  defineEmits([
    'squareClick'
  ])
</script>

<template>
  <button v-on:click="$emit('squareClick', index)">
    <slot></slot>
  </button>
</template>

<style scoped>
button {
  width: 32px;
  height: 32px;
}
</style>
