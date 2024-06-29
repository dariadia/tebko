<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'
const TOTAL = 32
const CORNERS = [0, 1, 5, 6]
let board = reactive([[0]])
onMounted(() => {
  board = Array.from({ length: 7 }, (_, row) =>
    Array.from({ length: 7 }, (_, col) => {
      if (row === 3 && col === 3) return 0
      if (CORNERS.includes(row) && CORNERS.includes(col)) return -1
      else return 1
    })
  )
})

// const publishedBooksMessage = computed(() => {
//   return author.books.length > 0 ? 'Yes' : 'No'
// })
const drag = ref(false)
</script>

<template>
  <header style="text-align: center">
    <h1>Ненецкая головоломка Тебко</h1>
    <small
      >Головоломка символизирует стадо оленей в тундре. Задача игрока - собрать стадо вместе, согнав
      их в центр.</small
    >
  </header>
  <main>
    {{ board }}
    <draggable
      v-model="board"
      group="people"
      @start="drag = true"
      @end="drag = false"
      item-key="id"
    >
      <template #item="{ element }">
        <div>{{ element.name }}</div>
      </template>
    </draggable>
  </main>
</template>
