<template>
  <div class="app">
    <input v-model="text" class="input" />

    <div
      class="grid"
      :style="{ gridTemplateColumns: `repeat(${cols}, ${CELL}px)` }"
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
const ROWS = 7;
const CELL = 12;
const GAP = 4;
const CHAR_WIDTH = 6;
const MAX_LEVEL = 4;

/* ===== STATE ===== */
const text = ref("WEZ SIE ZA ROBOTE");
const viewportWidth = ref(window.innerWidth);
const tick = ref(0);

/* ===== RESIZE + ANIMATION ===== */
const updateWidth = () => (viewportWidth.value = window.innerWidth);

onMounted(() => {
  window.addEventListener("resize", updateWidth);
  setInterval(() => tick.value++, 120);
});

onUnmounted(() => {
  window.removeEventListener("resize", updateWidth);
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

/* ===== COLS (FULL WIDTH) ===== */
const cols = computed(() => {
  const unit = CELL + GAP;
  return Math.floor((viewportWidth.value + GAP) / unit);
});

/* ===== GRID ===== */
const grid = computed(() => {
  const matrix = Array.from({ length: ROWS }, () =>
    Array(cols.value).fill(0)
  );

  const chars = text.value.toUpperCase();
  const textWidth = chars.length * CHAR_WIDTH - 1;
  let xOffset = Math.floor((cols.value - textWidth) / 2);

  chars.split("").forEach((ch, charIndex) => {
    const glyph = FONT[ch] || FONT[" "];

    for (let y = 0; y < ROWS; y++) {
      for (let x = 0; x < 5; x++) {
        if (glyph[y][x] === "1") {
          const wave =
            (Math.sin((tick.value + charIndex * 4 + y) / 3) + 1) / 2;
          const level = 1 + Math.floor(wave * (MAX_LEVEL - 1));

          const gx = xOffset + x;
          if (gx >= 0 && gx < cols.value) {
            matrix[y][gx] = level;
          }
        }
      }
    }
    xOffset += CHAR_WIDTH;
  });

  return matrix.flat();
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
  overflow-x: hidden;
}

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 24px;
}

.input {
  background: #161b22;
  border: 1px solid #30363d;
  color: #c9d1d9;
  padding: 10px 16px;
  border-radius: 10px;
  font-size: 15px;
}

.grid {
  width: 100vw;
  display: grid;
  grid-auto-rows: 12px;
  gap: 4px;
}

.cell {
  width: 12px;
  height: 12px;
  background: #161b22;
  border-radius: 3px;
}

.cell.l1 { background: #0e4429; }
.cell.l2 { background: #006d32; }
.cell.l3 { background: #26a641; }
.cell.l4 { background: #39d353; }
</style>
