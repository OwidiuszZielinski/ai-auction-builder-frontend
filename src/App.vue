<template>
  <div class="app">
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

    <div class="footer">
      <div class="input-container">
        <input
          v-model="text"
          class="input"
          placeholder="Wpisz tekst..."
          @keydown.enter="blurInput"
        />
        <div class="footer-text">
          Powered with <span class="heart">❤️</span> By Owidiusz
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from "vue";

/* ===== CONFIG ===== */
const CELL = ref(12);
const GAP = ref(4);
const MAX_LEVEL = 4;

/* ===== STATE ===== */
const text = ref("WEZ SIE ZA ROBOTE");
const viewportWidth = ref(window.innerWidth);
const viewportHeight = ref(window.innerHeight);
const tick = ref(0);
const isMobile = ref(false);

/* ===== RESPONSIVE ===== */
const checkMobile = () => {
  isMobile.value = window.innerWidth <= 768;
  CELL.value = isMobile.value ? 8 : 12;
  GAP.value = isMobile.value ? 2 : 4;
};

const updateViewport = () => {
  viewportWidth.value = window.innerWidth;
  viewportHeight.value = window.innerHeight;
  checkMobile();
};

const blurInput = (e) => e.target.blur();

/* ===== LIFECYCLE ===== */
onMounted(() => {
  checkMobile();
  window.addEventListener("resize", updateViewport);
  setInterval(() => tick.value++, isMobile.value ? 150 : 120);
});

onUnmounted(() => {
  window.removeEventListener("resize", updateViewport);
});

/* ===== FONT ===== */
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

/* ===== GRID SIZE ===== */
const cols = computed(() => {
  const unit = CELL.value + GAP.value;
  return Math.max(1, Math.floor((viewportWidth.value + GAP.value) / unit));
});

const rows = computed(() => {
  const unit = CELL.value + GAP.value;
  return Math.max(1, Math.floor((viewportHeight.value + GAP.value) / unit));
});

/* ===== DYNAMIC CHAR WIDTH ===== */
const charWidth = computed(() => {
  if (!isMobile.value) return 6;
  if (cols.value < 30) return 4;
  return 5;
});

/* ===== GRID RENDER ===== */
const grid = computed(() => {
  const cells = Array(cols.value * rows.value).fill(0);
  if (!text.value.trim()) return cells;

  const chars = text.value.toUpperCase();
  const cw = charWidth.value;

  const maxChars = Math.floor(cols.value / cw);
  const visibleChars = chars.slice(0, maxChars);

  const textWidth = visibleChars.length * cw - 1;
  const textHeight = 7;

  let startX = Math.max(0, Math.floor((cols.value - textWidth) / 2));
  const startY = Math.max(0, Math.floor((rows.value - textHeight) / 2) - 2);

  visibleChars.split("").forEach((ch, charIndex) => {
    const glyph = FONT[ch] || FONT[" "];

    for (let y = 0; y < 7; y++) {
      const gy = startY + y;
      if (gy < 0 || gy >= rows.value) continue;

      for (let x = 0; x < 5; x++) {
        if (glyph[y][x] === "1") {
          const wave = (Math.sin((tick.value + charIndex * 4 + y) / 3) + 1) / 2;
          const level = 1 + Math.floor(wave * (MAX_LEVEL - 1));

          const gx = startX + x;
          if (gx >= 0 && gx < cols.value) {
            cells[gy * cols.value + gx] = level;
          }
        }
      }
    }
    startX += cw;
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

watch(isMobile, updateViewport);
</script>

<style>
html, body {
  margin: 0;
  background: #0d1117;
  overflow: hidden;
  font-family: system-ui, sans-serif;
}

.app {
  position: fixed;
  inset: 0;
  display: flex;
  flex-direction: column;
}

.grid {
  flex: 1;
  display: grid;
  gap: 4px;
  padding: 4px;
}

.cell {
  background: #161b22;
  border-radius: 3px;
}

.cell.l1 { background: #0e4429; }
.cell.l2 { background: #006d32; }
.cell.l3 { background: #26a641; }
.cell.l4 { background: #39d353; }

.footer {
  background: rgba(13,17,23,.95);
  border-top: 1px solid #30363d;
  padding: 12px;
  display: flex;
  justify-content: center;
}

.input-container {
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 100%;
  max-width: 400px;
}

.input {
  background: #161b22;
  border: 1px solid #30363d;
  color: #c9d1d9;
  padding: 12px;
  border-radius: 10px;
  font-size: 16px;
}

.footer-text {
  color: #c9d1d9;
  font-size: 13px;
  text-align: center;
}

.heart {
  animation: heartbeat 1s infinite;
}

@keyframes heartbeat {
  0% { transform: scale(1); }
  25% { transform: scale(1.2); }
  50% { transform: scale(1); }
}
</style>
