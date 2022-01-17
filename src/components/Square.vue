<script setup>
  defineProps({
    index: String   // TODO: Should be of type Number, but this generates a warning
  })

  defineEmits([
    'squareClick'   // generated on click
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
