<template>
  <div class="info__item">row {{ row }} col {{ col }}</div>
</template>

<script setup>
import { toRefs, defineProps } from 'vue'

const props = defineProps({ row: Number, col: Number })
const { row, col } = toRefs(props)
</script>

<style scoped lang="scss">
.info__item {
  display: flex;
  align-items: center;
  height: 2.5rem;
  padding: 0 1rem;
  background-color: #fbf9e3;
  border: 1px solid #fcf2dd;
  border-radius: 0.5rem;
  font-size: 1rem;
  line-height: 1;
  color: #896d3b;
}
.info__item + .info__item {
  margin-top: 0.25rem;
}
</style>
