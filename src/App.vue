<template>
  <LoadingScreen v-if="isLoading" />
  <div v-else class="square">
    <div class="square__left">
      <div class="square__control">
        <div class="square__control-select">
          <v-select
            :options="modes"
            :reduce="(name) => name.field"
            placeholder="Pick mode"
            :clearable="false"
            :searchable="false"
            label="name"
            v-model="selectedMode"></v-select>
        </div>
        <div class="square__control-button">
          <button
            type="button"
            class="square__start"
            @click="squareStart"
            :disabled="!selectedMode">
            start
          </button>
        </div>
      </div>
      <Transition name="wrap">
        <div
          class="square__wrap"
          :style="{
            gridTemplateColumns: getGridTemplate(),
            gridTemplateRows: getGridTemplate(),
          }"
          v-if="isWrapVisible"
          @mouseenter="handleMouseEnter">
          <SquareItem
            v-for="(square, index) in squareItems"
            :key="square.id"
            :state="square.isActive"
            @toggle="toggleSquare(index)" />
        </div>
      </Transition>
    </div>
    <div class="square__right">
      <div class="square__header">Hover squares</div>
      <div class="square__info">
        <TransitionGroup name="info">
          <InfoItem
            v-for="item in infoItems.slice(-15).reverse()"
            :key="item.id"
            :row="item.row"
            :col="item.col" />
        </TransitionGroup>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import vSelect from 'vue-select'
import uniqid from 'uniqid'
import LoadingScreen from '@/components/LoadingScreen.vue'
import SquareItem from '@/components/SquareItem.vue'
import InfoItem from '@/components/InfoItem.vue'

const isLoading = ref(true)
const modes = ref([])
const selectedMode = ref()
const squareItems = ref(null)
const infoItems = ref([])
const gridSize = ref()
const isWrapVisible = ref(false)

const fetchModes = async () => {
  try {
    const response = await fetch(
      'https://60816d9073292b0017cdd833.mockapi.io/modes'
    )
    const data = await response.json()
    modes.value = data
    isLoading.value = false
  } catch (error) {
    console.error('Ошибка при загрузке режимов:', error)
  }
}

const toggleSquare = (index) => {
  squareItems.value[index].isActive = !squareItems.value[index].isActive

  defineInfo(index)
}

const defineInfo = (index) => {
  infoItems.value.push({
    id: uniqid(),
    row: Math.floor(index / gridSize.value) + 1,
    col: (index % gridSize.value) + 1,
  })
}

const getGridTemplate = () => {
  return `repeat(${gridSize.value}, 1fr)`
}

const handleMouseEnter = (event) => {
  if (event.target.classList.contains('square__item')) {
    const index = parseInt(event.target.dataset.index, 10)
    if (!isNaN(index)) {
      toggleSquare(index)
    }
  }
}

const squareStart = () => {
  infoItems.value = []
  isWrapVisible.value = false

  setTimeout(
    () => {
      squareItems.value = Array(selectedMode.value ** 2)
        .fill()
        .map(() => ({
          id: uniqid(),
          isActive: false,
        }))

      gridSize.value = selectedMode.value

      isWrapVisible.value = true
    },
    squareItems.value ? 600 : 0
  )
}

onMounted(() => {
  fetchModes()
})
</script>

<style lang="scss">
html,
body {
  margin: 0;
  padding: 0;
}
#app {
  min-height: 100dvh;
  padding: 2rem;
  box-sizing: border-box;
  font-family: sans-serif;
}
.square {
  display: grid;
  grid-template-columns: auto 250px;
  gap: 2rem;
  max-width: 800px;
  max-height: 100%;
  margin: 0 auto;

  &__control {
    display: grid;
    align-items: center;
    grid-template-columns: auto 200px;
    gap: 1rem;
    margin-bottom: 1rem;
  }

  &__wrap {
    display: grid;
    aspect-ratio: 1 / 1;
    width: 100%;
    border-top: 1px solid #000;
    border-left: 1px solid #000;
  }

  &__start {
    width: 100%;
    height: 2.5rem;
    background-color: #0075d8;
    border: none;
    border-radius: 0.5rem;
    font-size: 1.25rem;
    text-transform: uppercase;
    color: #ffffff;
    cursor: pointer;

    &:disabled {
      opacity: 0.5;
      cursor: default;
    }
  }

  &__header {
    height: 2.5rem;
    margin-bottom: 1rem;
    font-weight: 600;
    font-size: 2rem;
    line-height: 1.25;
  }

  &__info {
    height: 520px;
    overflow: scroll;
  }

  .v-select,
  .vs__dropdown-toggle {
    height: 2.5rem;
    cursor: pointer;
  }
  .vs__dropdown-toggle {
    border-radius: 0.5rem;
  }
  .vs--open .vs__dropdown-toggle {
    border-radius: 0.5rem 0.5rem 0 0;
  }
  .vs__dropdown-menu {
    border-radius: 0 0 0.5rem 0.5rem;
  }
  .vs__dropdown-option--highlight {
    background-color: #0075d8;
  }
}

.wrap-enter-active,
.wrap-leave-active {
  transition: opacity 0.6s ease;
}

.wrap-enter-from,
.wrap-leave-to {
  opacity: 0;
}

.info-enter-active,
.info-leave-active {
  transition: all 0.3s ease;
}
.info-enter-from,
.info-leave-to {
  opacity: 0;
  margin-top: -2.5rem;
}

@import url('~vue-select/dist/vue-select.css');
</style>
