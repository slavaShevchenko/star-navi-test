<template>
  <div
    class="square__item"
    :class="{ active: state }"
    @mouseenter="$emit('toggle', state)"></div>
</template>

<script setup>
import { toRefs, defineProps } from 'vue'

const props = defineProps({ state: Boolean })
const { state } = toRefs(props)
</script>

<style scoped lang="scss">
.square__item {
  background-color: transparent;
  border-right: 1px solid #000;
  border-bottom: 1px solid #000;
  transition: background-color 0.3s ease;
  will-change: background-color;

  &.active {
    background-color: #03a9f5;
  }
}
</style>
