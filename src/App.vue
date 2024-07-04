<script setup lang="ts">
import { ref, computed, type Ref } from 'vue'
import draggable from 'vuedraggable'

type DragEvent = Event & {
  oldIndex: number
  newIndex: number
  to: { dataset: Record<string, string> }
  from: { dataset: Record<string, string> }
}

const row0 = ref([-1, -1, 1, 1, 1, -1, -1])
const row1 = ref([-1, -1, 1, 1, 1, -1, -1])
const row2 = ref([1, 1, 1, 1, 1, 1, 1])
const row3 = ref([1, 1, 1, 0, 1, 1, 1])
const row4 = ref([1, 1, 1, 1, 1, 1, 1])
const row5 = ref([-1, -1, 1, 1, 1, -1, -1])
const row6 = ref([-1, -1, 1, 1, 1, -1, -1])
let possibleMoves: Ref<number[][]> = ref([[]])

const board = computed({
  get: () => [row0.value, row1.value, row2.value, row3.value, row4.value, row5.value, row6.value],
  set: (val) => {}
})

let carry: number[][] = []

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

const allowMove = ref(true)
const ruleBreak = ref(false)

const onMove = ref((event: DragEvent, row: number) => {
  drag.value = true
  const oldIndex = event.oldIndex
  const value = board.value[row][oldIndex]
  carry = [...board.value]
  if (value === -1 || value === 0) {
    hint.value = true
    allowMove.value = false
    setTimeout(() => (hint.value = false), 3000)
  } else if (value === 1) checkDirections(row, oldIndex)
})

const moveEnd = ref((event: DragEvent) => {
  const targetCol = event.newIndex
  const targetRow = Number(event.to?.dataset?.row)

  if (allowMove.value)
    allowMove.value = possibleMoves.value.some(
      (dir) => dir[0] === targetRow && dir[1] === targetCol
    )

  if (allowMove.value) {
    const fromCol = event.oldIndex
    const fromRow = Number(event.from?.dataset?.row)
    let midCol = fromCol
    let midRow = fromRow
    if (targetCol < fromCol) midCol = targetCol + 1
    else if (targetCol > fromCol) midCol = targetCol - 1

    if (targetRow < fromRow) midRow = targetRow + 1
    else if (targetRow > fromRow) midRow = targetRow - 1
    carry[midRow][midCol] = 0
    carry[fromRow][fromCol] = 0
    carry[targetRow][targetCol] = 1
  }
  row0.value = carry[0]
  row1.value = carry[1]
  row2.value = carry[2]
  row3.value = carry[3]
  row4.value = carry[4]
  row5.value = carry[5]
  row6.value = carry[6]
  drag.value = false
})
</script>

<template>
  <header style="text-align: center">
    <h1>Ненецкая головоломка Тебко</h1>
    <small
      >Головоломка символизирует стадо оленей в тундре. Задача игрока - собрать стадо вместе, согнав
      их в центр.</small
    >
    <v-spacer />
    <v-menu max-width="300" open-on-hover theme="dark">
      <template v-slot:activator="{ props }">
        <v-btn v-bind="props" class="mt-5"> Правила игры </v-btn>
      </template>
      <v-list>
        <v-list-item>
          <v-list-item-title><b>Правила игры</b></v-list-item-title>
          Ходить можно по вертикали и горизонтали (по диагонали запрещается), перешагивая через
          занятую клетку («олень»). «Оленя» через которого перешагнули, убирают с поля. Игра
          считается выигранной, если на доске останется только один «олень».
        </v-list-item>
      </v-list>
    </v-menu>
    <h4 v-if="!playing" class="mt-4">Вы выиграли! 🎉</h4>
  </header>
  <main class="pt-10 d-block mx-auto" style="width: fit-content">
    <v-fade-transition>
      <v-alert
        v-model="hint"
        position="absolute"
        color="warning"
        text="⚠︎ Вы можете двигать только оленей"
        class="py-2 px-4"
        style="
          top: calc((100vh - 50px) / 2);
          left: calc((100vw - 300px) / 2);
          cursor: pointer;
          background-color: var(--vt-c-black);
          z-index: 2;
        "
        variant="outlined"
        @click="hint = false"
      ></v-alert>
    </v-fade-transition>
    <v-fade-transition>
      <v-alert
        v-model="ruleBreak"
        position="absolute"
        color="warning"
        text="⚠︎ Так ходить нельзя! Проверьте подсказки в описании игры"
        class="py-2 px-4"
        style="
          top: calc((100vh - 50px) / 2);
          left: calc((100vw - 300px) / 2);
          cursor: pointer;
          background-color: var(--vt-c-black);
          z-index: 2;
        "
        variant="outlined"
        @click="hint = false"
      ></v-alert>
    </v-fade-transition>
    <draggable
      v-model="row0"
      group="rows"
      data-row="0"
      @start="(event) => onMove(event, 0)"
      @end="moveEnd"
      style="height: 56px; max-width: 392px; overflow: hidden"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-card
          :key="indx"
          tile
          plain
          max-width="56"
          variant="plain"
          max-height="56"
          class="py-4 my-0 px-6 border-surface border-sm d-inline-block w-auto"
        >
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-card>
      </template>
    </draggable>
    <draggable
      v-model="row1"
      data-row="1"
      group="rows"
      @start="(event) => onMove(event, 1)"
      @end="moveEnd"
      style="height: 56px; max-width: 392px; overflow: hidden"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-card
          :key="indx"
          tile
          plain
          max-width="56"
          variant="plain"
          max-height="56"
          class="py-4 my-0 px-6 border-surface border-sm d-inline-block w-auto"
        >
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-card>
      </template>
    </draggable>
    <draggable
      v-model="row2"
      data-row="2"
      group="rows"
      @start="(event) => onMove(event, 2)"
      @end="moveEnd"
      style="height: 56px; max-width: 392px; overflow: hidden"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-card
          :key="indx"
          tile
          plain
          max-width="56"
          variant="plain"
          max-height="56"
          class="py-4 my-0 px-6 border-surface border-sm d-inline-block w-auto"
        >
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-card>
      </template>
    </draggable>
    <draggable
      v-model="row3"
      data-row="3"
      group="rows"
      @start="(event) => onMove(event, 3)"
      @end="moveEnd"
      style="height: 56px; max-width: 392px; overflow: hidden"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-card
          :key="indx"
          tile
          plain
          max-width="56"
          variant="plain"
          max-height="56"
          class="py-4 my-0 px-6 border-surface border-sm d-inline-block w-auto"
        >
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-card>
      </template>
    </draggable>
    <draggable
      v-model="row4"
      data-row="4"
      group="rows"
      @start="(event) => onMove(event, 4)"
      @end="moveEnd"
      style="height: 56px; max-width: 392px; overflow: hidden"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-card
          :key="indx"
          tile
          plain
          max-width="56"
          variant="plain"
          max-height="56"
          class="py-4 my-0 px-6 border-surface border-sm d-inline-block w-auto"
        >
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-card>
      </template>
    </draggable>
    <draggable
      v-model="row5"
      data-row="5"
      group="rows"
      @start="(event) => onMove(event, 5)"
      @end="moveEnd"
      style="height: 56px; max-width: 392px; overflow: hidden"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-card
          :key="indx"
          tile
          plain
          max-width="56"
          variant="plain"
          max-height="56"
          class="py-4 my-0 px-6 border-surface border-sm d-inline-block w-auto"
        >
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-card>
      </template>
    </draggable>
    <draggable
      v-model="row6"
      data-row="6"
      group="rows"
      @start="(event) => onMove(event, 6)"
      @end="moveEnd"
      style="height: 56px; max-width: 392px; overflow: hidden"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <v-card
          :key="indx"
          tile
          plain
          max-width="56"
          variant="plain"
          max-height="56"
          class="py-4 my-0 px-6 border-surface border-sm d-inline-block w-auto"
        >
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </v-card>
      </template>
    </draggable>
  </main>
</template>
<style>
@media screen and (max-width: 450px) {
  .v-card.d-inline-block {
    max-width: 48px !important;
    max-height: 48px !important;
    padding: 8px 16px !important;
  }
  main > div {
    height: 48px !important;
  }
}
</style>
