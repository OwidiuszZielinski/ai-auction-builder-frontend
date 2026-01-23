<template>
  <div class="app">
    <!-- Main grid covering entire page -->
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

    <!-- Footer with input -->
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
const CHAR_WIDTH = 6;
const MAX_LEVEL = 4;

/* ===== STATE ===== */
const text = ref("WEZ SIE ZA ROBOTE");
const viewportWidth = ref(window.innerWidth);
const viewportHeight = ref(window.innerHeight);
const tick = ref(0);
const heartBeat = ref(0);
const isMobile = ref(false);

/* ===== RESPONSIVE ADJUSTMENTS ===== */
const checkMobile = () => {
  isMobile.value = window.innerWidth <= 768;
  
  // Adjust grid size for mobile
  if (isMobile.value) {
    CELL.value = 8;
    GAP.value = 2;
  } else {
    CELL.value = 12;
    GAP.value = 4;
  }
};

/* ===== RESIZE + ANIMATION ===== */
const updateViewport = () => {
  viewportWidth.value = window.innerWidth;
  viewportHeight.value = window.innerHeight;
  checkMobile();
};

const blurInput = (e) => {
  e.target.blur();
};

onMounted(() => {
  checkMobile();
  window.addEventListener("resize", updateViewport);
  
  // Main animation tick (slower on mobile for performance)
  const animationInterval = isMobile.value ? 150 : 120;
  setInterval(() => tick.value++, animationInterval);
  
  // Heartbeat animation
  setInterval(() => {
    heartBeat.value = (heartBeat.value + 1) % 2;
  }, 500);
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
  const unit = CELL.value + GAP.value;
  return Math.max(1, Math.floor((viewportWidth.value + GAP.value) / unit));
});

const rows = computed(() => {
  const unit = CELL.value + GAP.value;
  return Math.max(1, Math.floor((viewportHeight.value + GAP.value) / unit));
});

/* ===== GRID ===== */
const grid = computed(() => {
  const totalCells = cols.value * rows.value;
  const cells = Array(totalCells).fill(0);

  if (!text.value.trim()) return cells;

  const chars = text.value.toUpperCase();
  const textWidth = chars.length * CHAR_WIDTH - 1;
  const textHeight = 7;
  
  // Adjust vertical position for mobile (higher up to avoid keyboard)
  const verticalOffset = isMobile.value ? -20 : -10;
  let startX = Math.floor((cols.value - textWidth) / 2);
  const startY = Math.floor((rows.value - textHeight) / 2) + verticalOffset;
  
  // Don't render if text would be out of bounds
  if (startY < 0 || startY >= rows.value) return cells;
  
  chars.split("").forEach((ch, charIndex) => {
    const glyph = FONT[ch] || FONT[" "];

    for (let y = 0; y < 7; y++) {
      const gridY = startY + y;
      if (gridY < 0 || gridY >= rows.value) continue;
      
      for (let x = 0; x < 5; x++) {
        if (glyph[y][x] === "1") {
          // Smoother wave effect on mobile
          const waveSpeed = isMobile.value ? 4 : 3;
          const wave = (Math.sin((tick.value + charIndex * 4 + y) / waveSpeed) + 1) / 2;
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

// Watch for mobile changes to adjust grid
watch(isMobile, () => {
  updateViewport();
});
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
  background: #0d1117;
  overflow: hidden;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  -webkit-text-size-adjust: 100%;
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}

.app {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
}

/* Grid covering entire page */
.grid {
  flex: 1;
  display: grid;
  gap: 4px;
  padding: 4px;
  box-sizing: border-box;
  overflow: hidden;
}

/* Footer at the bottom */
.footer {
  background: rgba(13, 17, 23, 0.95);
  border-top: 1px solid #30363d;
  padding: 15px 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  flex-shrink: 0;
}

.input-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  max-width: 600px;
  width: 100%;
}

.input {
  background: #161b22;
  border: 1px solid #30363d;
  color: #c9d1d9;
  padding: 12px 16px;
  border-radius: 12px;
  font-size: 16px;
  width: 100%;
  max-width: 400px;
  transition: all 0.3s ease;
  -webkit-appearance: none;
  appearance: none;
}

.input:focus {
  outline: none;
  border-color: #238636;
  box-shadow: 0 0 0 3px rgba(35, 134, 54, 0.3);
}

.input::placeholder {
  color: #8b949e;
}

.footer-text {
  color: #c9d1d9;
  font-size: 14px;
  display: flex;
  align-items: center;
  gap: 8px;
  text-align: center;
}

.heart {
  display: inline-block;
  animation: heartbeat 1s infinite;
  color: #ff6b6b;
  filter: drop-shadow(0 0 8px rgba(255, 107, 107, 0.6));
}

@keyframes heartbeat {
  0% {
    transform: scale(1);
    color: #ff6b6b;
  }
  25% {
    transform: scale(1.2);
    color: #ff3838;
  }
  50% {
    transform: scale(1);
    color: #ff6b6b;
  }
  75% {
    transform: scale(1.1);
    color: #ff3838;
  }
  100% {
    transform: scale(1);
    color: #ff6b6b;
  }
}

.cell {
  background: #161b22;
  border-radius: 3px;
  width: 100%;
  height: 100%;
  transition: background-color 0.3s ease;
}

.cell.l1 { background: #0e4429; }
.cell.l2 { background: #006d32; }
.cell.l3 { background: #26a641; }
.cell.l4 { background: #39d353; }

/* Mobile optimizations */
@media (max-width: 768px) {
  .grid {
    gap: 2px;
    padding: 2px;
  }
  
  .cell {
    border-radius: 2px;
  }
  
  .footer {
    padding: 12px 8px;
  }
  
  .input {
    padding: 10px 14px;
    font-size: 16px;
    border-radius: 10px;
  }
  
  .input-container {
    gap: 10px;
  }
  
  .footer-text {
    font-size: 13px;
    flex-wrap: wrap;
    justify-content: center;
  }
  
  .heart {
    font-size: 1.2em;
  }
}

/* Prevent zoom on input focus on mobile */
@media (max-width: 768px) {
  input, textarea, select {
    font-size: 16px !important;
  }
}

/* Very small screens */
@media (max-width: 360px) {
  .footer {
    padding: 10px 6px;
  }
  
  .input {
    padding: 8px 12px;
    font-size: 15px;
  }
  
  .footer-text {
    font-size: 12px;
  }
}

/* Landscape orientation */
@media (max-height: 500px) and (orientation: landscape) {
  .footer {
    padding: 8px;
    flex-direction: row;
  }
  
  .input-container {
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    gap: 15px;
  }
  
  .input {
    max-width: 200px;
    margin-bottom: 0;
  }
  
  .footer-text {
    margin-bottom: 0;
    white-space: nowrap;
  }
}
</style>
