<script setup lang="ts">
import { ref, computed } from 'vue'
import draggable from 'vuedraggable'

const row0 = ref([-1, -1, 1, 1, 1, -1, -1])
const row1 = ref([-1, -1, 1, 1, 1, -1, -1])
const row2 = ref([1, 1, 1, 1, 1, 1, 1])
const row3 = ref([1, 1, 1, 0, 1, 1, 1])
const row4 = ref([1, 1, 1, 1, 1, 1, 1])
const row5 = ref([-1, -1, 1, 1, 1, -1, -1])
const row6 = ref([-1, -1, 1, 1, 1, -1, -1])

const board = computed(() => {
  return [row0.value, row1.value, row2.value, row3.value, row4.value, row5.value, row6.value]
})

const _board = computed(() => board)

const playing = computed(() => {
  return board.value.some((row) => row.some((piece) => piece === 1))
})
let drag = ref(false)
const onMove = ref((event: Event, row: number) => {
  drag.value = true

  console.log(event, row)
  return false
})
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
    <draggable
      v-model="row0"
      group="rows"
      @start="(event) => onMove(event, 0)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <div :key="indx" style="padding: 20px 26px; border: 1px solid grey; display: inline-block">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </div>
      </template>
    </draggable>
    <draggable
      v-model="row1"
      group="rows"
      @start="(event) => onMove(event, 1)"
      @end="drag = false"
      item-key="col"
    >
      <template #item="{ element, indx }">
        <div :key="indx" style="padding: 20px 26px; border: 1px solid grey; display: inline-block">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </div>
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
        <div :key="indx" style="padding: 20px 26px; border: 1px solid grey; display: inline-block">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </div>
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
        <div :key="indx" style="padding: 20px 26px; border: 1px solid grey; display: inline-block">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </div>
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
        <div :key="indx" style="padding: 20px 26px; border: 1px solid grey; display: inline-block">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </div>
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
        <div :key="indx" style="padding: 20px 26px; border: 1px solid grey; display: inline-block">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </div>
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
        <div :key="indx" style="padding: 20px 26px; border: 1px solid grey; display: inline-block">
          <span v-if="element !== -1">{{ element }}</span>
          <span v-else style="opacity: 0">1</span>
        </div>
      </template>
    </draggable>
  </main>
</template>
