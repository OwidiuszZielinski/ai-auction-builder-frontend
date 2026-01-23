<template>
  <div class="app">
    <input v-model="text" class="input" />

    <div
      class="grid"
      :style="{
        gridTemplateColumns: `repeat(${cols}, ${CELL}px)`,
        gridTemplateRows: `repeat(${rows}, ${CELL}px)`
      }"
    >
      <div
        v-for="(cell, i) in grid"
        :key="i"
        class="cell"
        :class="cellClass(cell)"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from "vue";

/* ===== CONFIG ===== */
const CELL = 12;
const GAP = 4;
const CHAR_WIDTH = 6;
const MAX_LEVEL = 4;

/* ===== STATE ===== */
const text = ref("WEZ SIE ZA ROBOTE");
const viewportWidth = ref(window.innerWidth);
const viewportHeight = ref(window.innerHeight);
const tick = ref(0);

/* ===== RESIZE + ANIMATION ===== */
const updateViewport = () => {
  viewportWidth.value = window.innerWidth;
  viewportHeight.value = window.innerHeight;
};

onMounted(() => {
  window.addEventListener("resize", updateViewport);
  setInterval(() => tick.value++, 120);
});

onUnmounted(() => {
  window.removeEventListener("resize", updateViewport);
});

/* ===== FULL 5x7 FONT (A–Z + space) ===== */
const FONT = {
  A:["01110","10001","10001","11111","10001","10001","10001"],
  B:["11110","10001","10001","11110","10001","10001","11110"],
  C:["01111","10000","10000","10000","10000","10000","01111"],
  D:["11110","10001","10001","10001","10001","10001","11110"],
  E:["11111","10000","10000","11110","10000","10000","11111"],
  F:["11111","10000","10000","11110","10000","10000","10000"],
  G:["01111","10000","10000","10111","10001","10001","01111"],
  H:["10001","10001","10001","11111","10001","10001","10001"],
  I:["11111","00100","00100","00100","00100","00100","11111"],
  J:["00111","00010","00010","00010","10010","10010","01100"],
  K:["10001","10010","10100","11000","10100","10010","10001"],
  L:["10000","10000","10000","10000","10000","10000","11111"],
  M:["10001","11011","10101","10101","10001","10001","10001"],
  N:["10001","11001","10101","10011","10001","10001","10001"],
  O:["01110","10001","10001","10001","10001","10001","01110"],
  P:["11110","10001","10001","11110","10000","10000","10000"],
  Q:["01110","10001","10001","10001","10101","10010","01101"],
  R:["11110","10001","10001","11110","10100","10010","10001"],
  S:["01111","10000","10000","01110","00001","00001","11110"],
  T:["11111","00100","00100","00100","00100","00100","00100"],
  U:["10001","10001","10001","10001","10001","10001","01110"],
  V:["10001","10001","10001","10001","10001","01010","00100"],
  W:["10001","10001","10001","10101","10101","10101","01010"],
  X:["10001","01010","00100","00100","00100","01010","10001"],
  Y:["10001","01010","00100","00100","00100","00100","00100"],
  Z:["11111","00001","00010","00100","01000","10000","11111"],
  " ":["00000","00000","00000","00000","00000","00000","00000"],
};

/* ===== COLS & ROWS (FULL VIEWPORT) ===== */
const cols = computed(() => {
  const unit = CELL + GAP;
  return Math.floor((viewportWidth.value + GAP) / unit);
});

const rows = computed(() => {
  const unit = CELL + GAP;
  return Math.floor((viewportHeight.value + GAP) / unit);
});

/* ===== GRID ===== */
const grid = computed(() => {
  const totalCells = cols.value * rows.value;
  const cells = Array(totalCells).fill(0);

  const chars = text.value.toUpperCase();
  const textWidth = chars.length * CHAR_WIDTH - 1;
  const textHeight = 7; // wysokość czcionki
  
  // Oblicz środkową pozycję tekstu
  let startX = Math.floor((cols.value - textWidth) / 2);
  const startY = Math.floor((rows.value - textHeight) / 2);
  
  // Zabezpieczenie przed ujemnymi wartościami
  if (startY < 0 || startY >= rows.value) return cells;
  
  chars.split("").forEach((ch, charIndex) => {
    const glyph = FONT[ch] || FONT[" "];

    for (let y = 0; y < 7; y++) {
      const gridY = startY + y;
      if (gridY < 0 || gridY >= rows.value) continue;
      
      for (let x = 0; x < 5; x++) {
        if (glyph[y][x] === "1") {
          const wave = (Math.sin((tick.value + charIndex * 4 + y) / 3) + 1) / 2;
          const level = 1 + Math.floor(wave * (MAX_LEVEL - 1));

          const gridX = startX + x;
          
          if (gridX >= 0 && gridX < cols.value) {
            const index = gridY * cols.value + gridX;
            cells[index] = level;
          }
        }
      }
    }
    startX += CHAR_WIDTH;
  });

  return cells;
});

/* ===== COLORS ===== */
const cellClass = (v) => ({
  l1: v === 1,
  l2: v === 2,
  l3: v === 3,
  l4: v === 4,
});
</script>

<style>
html, body {
  margin: 0;
  background: #0d1117;
  overflow: hidden;
}

.app {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 24px;
  z-index: 2;
}

.input {
  background: #161b22;
  border: 1px solid #30363d;
  color: #c9d1d9;
  padding: 10px 16px;
  border-radius: 10px;
  font-size: 15px;
  position: relative;
  z-index: 3;
  width: 300px;
}

.grid {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: grid;
  gap: 4px;
  padding: 4px;
  box-sizing: border-box;
  z-index: 1;
}

.cell {
  background: #161b22;
  border-radius: 3px;
  width: 100%;
  height: 100%;
}

.cell.l1 { background: #0e4429; }
.cell.l2 { background: #006d32; }
.cell.l3 { background: #26a641; }
.cell.l4 { background: #39d353; }
</style>
