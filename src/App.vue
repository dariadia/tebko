<script setup lang="ts">
import { ref, computed, type Ref } from 'vue'
import draggable from 'vuedraggable'

const row0 = ref([-1, -1, 1, 1, 1, -1, -1])
const row1 = ref([-1, -1, 1, 1, 1, -1, -1])
const row2 = ref([1, 1, 1, 1, 1, 1, 1])
const row3 = ref([1, 1, 1, 0, 1, 1, 1])
const row4 = ref([1, 1, 1, 1, 1, 1, 1])
const row5 = ref([-1, -1, 1, 1, 1, -1, -1])
const row6 = ref([-1, -1, 1, 1, 1, -1, -1])
let possibleMoves: Ref<number[][]> = ref([[]])

const board = computed(() => {
  return [row0.value, row1.value, row2.value, row3.value, row4.value, row5.value, row6.value]
})

const _board = ref([...board.value])

const playing = computed(() => {
  return board.value.some((row) => row.some((piece) => piece === 1))
})
const drag = ref(false)
const hint = ref(false)
const checkDirections = (row: number, col: number) => {
  possibleMoves.value = []
  const DIRECTIONS = [
    [-1, 0],
    [1, 0],
    [0, -1],
    [0, 1]
  ]
  DIRECTIONS.forEach((dir) => {
    const ROW_LEN = 7
    if (
      [row + dir[0], col + dir[1], row + dir[0] * 2, col + dir[1] * 2].some(
        (item) => item >= ROW_LEN || item < 0
      )
    )
      return false
    if (
      board.value[row + dir[0]][col + dir[1]] &&
      board.value[row + dir[0] * 2][col + dir[1] * 2] === 0
    )
      possibleMoves.value.push([row + dir[0] * 2, col + dir[1] * 2])
  })
  if (possibleMoves.value.length) return true
  else return false
}

const onMove = ref(
  (event: Event & { oldIndex: number; to: { dataset: Record<string, string> } }, row: number) => {
    drag.value = true
    const oldIndex = event.oldIndex
    const value = board.value[row][oldIndex]
    if (value === -1 || value === 0) hint.value = true
    else checkDirections(row, oldIndex)
    console.log(possibleMoves.value)
    // if (value === 1) {
    //   const targetRow = Number(event.to?.dataset?.row)
    //   if (board.value[targetRow]?.length )
    // }
  }
)
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
  <main style="padding-top: 42px; max-width: fit-content; margin: auto; display: block">
    <v-alert
      v-if="hint"
      closable
      absolute
      type="warning"
      text="Вы можете двигать только оленей"
      variant="outlined"
    ></v-alert>
    <draggable
      v-model="row0"
      group="rows"
      data-row="0"
      @start="(event) => onMove(event, 0)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-col :key="indx" class="py-5 px-6 border-surface border-sm d-inline-block w-auto">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-col>
      </template>
    </draggable>
    <draggable
      v-model="row1"
      data-row="1"
      group="rows"
      @start="(event) => onMove(event, 1)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-col :key="indx" class="py-5 px-6 border-surface border-sm d-inline-block w-auto">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-col>
      </template>
    </draggable>
    <draggable
      v-model="row2"
      group="rows"
      @start="(event) => onMove(event, 2)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-col :key="indx" class="py-5 px-6 border-surface border-sm d-inline-block w-auto">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-col>
      </template>
    </draggable>
    <draggable
      v-model="row3"
      group="rows"
      @start="(event) => onMove(event, 3)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-col :key="indx" class="py-5 px-6 border-surface border-sm d-inline-block w-auto">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-col>
      </template>
    </draggable>
    <draggable
      v-model="row4"
      group="rows"
      @start="(event) => onMove(event, 4)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-col :key="indx" class="py-5 px-6 border-surface border-sm d-inline-block w-auto">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-col>
      </template>
    </draggable>
    <draggable
      v-model="row5"
      group="rows"
      @start="(event) => onMove(event, 5)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-col :key="indx" class="py-5 px-6 border-surface border-sm d-inline-block w-auto">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-col>
      </template>
    </draggable>
    <draggable
      v-model="row6"
      group="rows"
      @start="(event) => onMove(event, 6)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-col :key="indx" class="py-5 px-6 border-surface border-sm d-inline-block w-auto">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-col>
      </template>
    </draggable>
  </main>
</template>
