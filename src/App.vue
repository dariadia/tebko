<script setup lang="ts">
import { ref, reactive, onBeforeMount, computed } from 'vue'
import nestedDraggable from 'vuedraggable'

const CORNERS = [0, 1, 5, 6]
let board = reactive([[1]])

onBeforeMount(() => {
  board = Array.from({ length: 7 }, (_, row) =>
    Array.from({ length: 7 }, (_, col) => {
      if (row === 3 && col === 3) return 0
      if (CORNERS.includes(row) && CORNERS.includes(col)) return -1
      else return 1
    })
  )
})
const playing = computed(() => {
  return board.some((row) => row.some((piece) => piece === 1))
})
const drag = ref(false)
</script>

<template>
  <header style="text-align: center">
    <h1>Ненецкая головоломка Тебко</h1>
    <small
      >Головоломка символизирует стадо оленей в тундре. Задача игрока - собрать стадо вместе, согнав
      их в центр.</small
    >
    <h4 v-if="!playing">Вы выиграли</h4>
  </header>
  <main style="padding-top: 100px; max-width: 900px; margin: auto; display: block">
    <nested-draggable v-model="board" @start="drag = true" @end="drag = false" item-key="id">
      <template #item="{ element }">
        <v-row style="display: block">
          <v-col v-for="item in element" style="padding: 36px; border: 1px solid grey">{{
            item
          }}</v-col>
        </v-row>
      </template>
    </nested-draggable>
  </main>
</template>
