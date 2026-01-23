<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const canvasRef = ref(null);
const TEXT = "Wez sie za robote";

function draw() {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const w = window.innerWidth;
  const h = window.innerHeight;
  const dpr = Math.min(window.devicePixelRatio || 1, 2);

  // ===== MAIN CANVAS =====
  canvas.width = w * dpr;
  canvas.height = h * dpr;
  canvas.style.width = w + "px";
  canvas.style.height = h + "px";

  const ctx = canvas.getContext("2d");
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);

  // ===== GRID =====
  const cell = Math.max(10, Math.floor(Math.min(w, h) / 55));
  const gap = 2;
  const size = cell - gap;

  const cols = Math.ceil(w / cell);
  const rows = Math.ceil(h / cell);

  // ===== BITMAP TEXT MASK (RETRO) =====
  const textScale = 6;
  const textCanvas = document.createElement("canvas");
  const tctx = textCanvas.getContext("2d");

  const textW = Math.floor(w / textScale);
  const textH = Math.floor(h / textScale);

  textCanvas.width = textW;
  textCanvas.height = textH;

  tctx.fillStyle = "black";
  tctx.fillRect(0, 0, textW, textH);

  const fontSize = Math.floor(textH * 0.5);
  tctx.fillStyle = "white";
  tctx.font = `900 ${fontSize}px monospace`;
  tctx.textAlign = "center";
  tctx.textBaseline = "middle";
  tctx.fillText(TEXT, textW / 2, textH / 2);

  // ===== COLORS =====
  const DARK_CELL = "#0d1117";
  const GRID_LINE = "#161b22";
  const GREENS = ["#0e4429", "#006d32", "#26a641", "#39d353"];

  // ===== CLEAR =====
  ctx.fillStyle = "#000";
  ctx.fillRect(0, 0, w, h);

  // ===== DRAW GRID + TEXT =====
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      const x = col * cell + gap / 2;
      const y = row * cell + gap / 2;

      // dark cell
      ctx.fillStyle = DARK_CELL;
      ctx.fillRect(x, y, size, size);
      ctx.strokeStyle = GRID_LINE;
      ctx.strokeRect(x, y, size, size);

      // === MAP TEXT: 2 CELLS WIDE ===
      const sx = Math.floor(col / 2);
      const sy = Math.floor(row);

      if (sx < textW && sy < textH) {
        const data = tctx.getImageData(sx, sy, 1, 1).data;
        if (data[0] > 200) {
          ctx.fillStyle =
            GREENS[Math.floor(Math.random() * GREENS.length)];
          ctx.fillRect(x, y, size, size);
        }
      }
    }
  }
}

onMounted(() => {
  draw();
  window.addEventListener("resize", draw);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", draw);
});
</script>

<template>
  <canvas ref="canvasRef"></canvas>
</template>

<style>
html,
body {
  margin: 0;
  width: 100%;
  height: 100%;
  background: #000;
  overflow: hidden;
}

canvas {
  display: block;
}
</style>
