<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const canvasRef = ref(null);
const TEXT = "WEZ SIE ZA ROBOTE";

// ===== RETRO FONT (5x7, grubość x2) =====
const FONT = {
  W: ["10101","10101","10101","10101","10101","10101","01010"],
  E: ["11111","10000","11110","10000","10000","10000","11111"],
  Z: ["11111","00001","00010","00100","01000","10000","11111"],
  S: ["01111","10000","10000","01110","00001","00001","11110"],
  I: ["111","010","010","010","010","010","111"],
  A: ["01110","10001","10001","11111","10001","10001","10001"],
  R: ["11110","10001","10001","11110","10100","10010","10001"],
  O: ["01110","10001","10001","10001","10001","10001","01110"],
  B: ["11110","10001","10001","11110","10001","10001","11110"],
  T: ["11111","00100","00100","00100","00100","00100","00100"],
  " ": ["000","000","000","000","000","000","000"]
};

let ctx, w, h;
let flicker = 0.9;
let flickerTimer;

function draw() {
  ctx.fillStyle = "#000";
  ctx.fillRect(0, 0, w, h);

  const cell = 14;
  const gap = 2;
  const size = cell - gap;

  const startX = Math.floor((w - TEXT.length * 8 * cell) / 2);
  const startY = Math.floor(h / 2 - 7 * cell / 2);

  // GRID BACKGROUND
  for (let y = 0; y < h; y += cell) {
    for (let x = 0; x < w; x += cell) {
      ctx.fillStyle = "#0d1117";
      ctx.fillRect(x, y, size, size);
    }
  }

  // DRAW TEXT
  let cursorX = startX;

  for (const char of TEXT) {
    const glyph = FONT[char] || FONT[" "];

    glyph.forEach((row, y) => {
      [...row].forEach((bit, x) => {
        if (bit === "1" && Math.random() < flicker) {
          // 2 kratki szerokości
          ctx.fillStyle = "#26a641";
          ctx.fillRect(
            cursorX + x * cell * 2,
            startY + y * cell,
            size * 2,
            size
          );
        }
      });
    });

    cursorX += cell * 8;
  }
}

// ===== SLOW FLICKER =====
function startFlicker() {
  flickerTimer = setInterval(() => {
    flicker = 0.85 + Math.random() * 0.15;
    draw();
  }, 500);
}

onMounted(() => {
  const canvas = canvasRef.value;
  w = window.innerWidth;
  h = window.innerHeight;
  canvas.width = w;
  canvas.height = h;
  ctx = canvas.getContext("2d");

  draw();
  startFlicker();
});

onBeforeUnmount(() => {
  clearInterval(flickerTimer);
});
</script>

<template>
  <canvas ref="canvasRef"></canvas>
</template>

<style>
html, body {
  margin: 0;
  width: 100%;
  height: 100%;
  background: black;
  overflow: hidden;
}
canvas {
  display: block;
}
</style>
