<template>
  <div class="app">
    <h2>Git Commit Flow Text</h2>

    <input
      v-model="text"
      placeholder="Wpisz tekst (A–Z)"
      maxlength="20"
    />

    <div class="grid">
      <div
        v-for="(cell, index) in grid"
        :key="index"
        class="cell"
        :class="{ active: cell }"
      />
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";

/**
 * GitHub-like grid
 */
const ROWS = 7;
const COLS = 53;

const text = ref("HELLO");

/**
 * Font 5x7 (A–Z + space)
 * 1 = pixel ON
 */
const FONT = {
  A: [
    "01110",
    "10001",
    "10001",
    "11111",
    "10001",
    "10001",
    "10001",
  ],
  E: [
    "11111",
    "10000",
    "10000",
    "11110",
    "10000",
    "10000",
    "11111",
  ],
  H: [
    "10001",
    "10001",
    "10001",
    "11111",
    "10001",
    "10001",
    "10001",
  ],
  L: [
    "10000",
    "10000",
    "10000",
    "10000",
    "10000",
    "10000",
    "11111",
  ],
  O: [
    "01110",
    "10001",
    "10001",
    "10001",
    "10001",
    "10001",
    "01110",
  ],
  " ": [
    "00000",
    "00000",
    "00000",
    "00000",
    "00000",
    "00000",
    "00000",
  ],
};

/**
 * Render text into grid
 */
const grid = computed(() => {
  // empty grid
  const matrix = Array.from({ length: ROWS }, () =>
    Array(COLS).fill(false)
  );

  let xOffset = 0;

  for (const char of text.value.toUpperCase()) {
    const glyph = FONT[char] || FONT[" "];

    for (let y = 0; y < ROWS; y++) {
      for (let x = 0; x < 5; x++) {
        if (glyph[y][x] === "1") {
          if (xOffset + x < COLS) {
            matrix[y][xOffset + x] = true;
          }
        }
      }
    }

    xOffset += 6; // 5 px + 1 space
    if (xOffset >= COLS) break;
  }

  return matrix.flat();
});
</script>

<style scoped>
.app {
  background: #0d1117;
  min-height: 100vh;
  color: #c9d1d9;
  padding: 24px;
  font-family: system-ui, sans-serif;
}

input {
  margin-bottom: 16px;
  padding: 8px 12px;
  background: #161b22;
  border: 1px solid #30363d;
  color: #c9d1d9;
  border-radius: 6px;
}

.grid {
  display: grid;
  grid-template-columns: repeat(53, 12px);
  grid-template-rows: repeat(7, 12px);
  gap: 3px;
}

.cell {
  width: 12px;
  height: 12px;
  background: #161b22;
  border-radius: 2px;
}

.cell.active {
  background: #2ea043;
}
</style>
