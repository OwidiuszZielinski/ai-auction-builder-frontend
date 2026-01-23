<template>
  <div class="app">
    <input
      v-model="text"
      class="input"
      placeholder="WPISZ TEKST"
    />

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
import { computed, ref, onMounted, onUnmounted } from "vue";

/* ===== CONFIG ===== */
const ROWS = 7;
const CELL = 12;
const GAP = 4;
const CHAR_WIDTH = 6;

const text = ref("WEZ SIE ZA ROBOTE");
const screenWidth = ref(window.innerWidth);

/* ===== RESIZE ===== */
const onResize = () => {
  screenWidth.value = window.innerWidth;
};

onMounted(() => {
  window.addEventListener("resize", onResize);
  startFlicker();
});
onUnmounted(() => window.removeEventListener("resize", onResize));

/* ===== FONT 5x7 ===== */
const FONT = {
  W:["10001","10001","10001","10101","10101","10101","01010"],
  E:["11111","10000","10000","11110","10000","10000","11111"],
  Z:["11111","00001","00010","00100","01000","10000","11111"],
  S:["01111","10000","10000","01110","00001","00001","11110"],
  I:["11111","00100","00100","00100","00100","00100","11111"],
  A:["01110","10001","10001","11111","10001","10001","10001"],
  R:["11110","10001","10001","11110","10100","10010","10001"],
  O:["01110","10001","10001","10001","10001","10001","01110"],
  B:["11110","10001","10001","11110","10001","10001","11110"],
  T:["11111","00100","00100","00100","00100","00100","00100"],
  " ":["00000","00000","00000","00000","00000","00000","00000"],
};

/* ===== GRID WIDTH (FULL SCREEN) ===== */
const cols = computed(() => {
  const perCell = CELL + GAP;
  return Math.floor(screenWidth.value / perCell);
});

/* ===== FLICKER MAP ===== */
const flicker = ref([]);

const startFlicker = () => {
  flicker.value = Array.from({ length: ROWS * cols.value }, () =>
    Math.random() < 0.08 ? randomLevel() : 0
  );

  setInterval(() => {
    flicker.value = flicker.value.map(() =>
      Math.random() < 0.03 ? randomLevel() : 0
    );
  }, 700);
};

const randomLevel = () => Math.floor(Math.random() * 4) + 1;

/* ===== RENDER ===== */
const grid = computed(() => {
  const matrix = Array.from({ length: ROWS }, () =>
    Array(cols.value).fill(0)
  );

  // background flicker
  flicker.value.forEach((v, i) => {
    const y = Math.floor(i / cols.value);
    const x = i % cols.value;
    if (matrix[y]) matrix[y][x] = v;
  });

  // text
  const chars = text.value.toUpperCase();
  const textWidth = chars.length * CHAR_WIDTH - 1;
  let xOffset = Math.floor((cols.value - textWidth) / 2);

  for (const ch of chars) {
    const glyph = FONT[ch] || FONT[" "];

    for (let y = 0; y < ROWS; y++) {
      for (let x = 0; x < 5; x++) {
        if (glyph[y][x] === "1") {
          matrix[y][xOffset + x] = 4; // max intensity
        }
      }
    }
    xOffset += CHAR_WIDTH;
  }

  return matrix.flat();
});

/* ===== COLORS ===== */
const cellClass = (level) => {
  return {
    "l1": level === 1,
    "l2": level === 2,
    "l3": level === 3,
    "l4": level === 4,
  };
};
</script>

<style>
html, body {
  margin: 0;
  background: #0d1117;
}

.app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 28px;
}

.input {
  background: #161b22;
  border: 1px solid #30363d;
  color: #c9d1d9;
  padding: 12px 18px;
  font-size: 16px;
  border-radius: 10px;
  outline: none;
}

.grid {
  display: grid;
  grid-auto-rows: 12px;
  gap: 4px;
  width: 100vw;
  justify-content: center;
}

.cell {
  width: 12px;
  height: 12px;
  background: #161b22;
  border-radius: 3px;
}

/* GitHub-like greens */
.cell.l1 { background: #0e4429; }
.cell.l2 { background: #006d32; }
.cell.l3 { background: #26a641; }
.cell.l4 { background: #39d353; }
</style>
