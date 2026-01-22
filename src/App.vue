<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const canvasRef = ref(null);

const TEXT = "example";

function draw() {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  const w = window.innerWidth;
  const h = window.innerHeight;

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

  // ===== TEXT MASK =====
  const mask = document.createElement("canvas");
  mask.width = w * dpr;
  mask.height = h * dpr;

  const mctx = mask.getContext("2d");
  mctx.setTransform(dpr, 0, 0, dpr, 0, 0);

  const fontSize = Math.floor(Math.min(w, h) * 0.25);
  mctx.fillStyle = "black";
  mctx.fillRect(0, 0, w, h);
  mctx.fillStyle = "white";
  mctx.font = `900 ${fontSize}px system-ui, -apple-system, Segoe UI, Roboto`;
  mctx.textAlign = "center";
  mctx.textBaseline = "middle";
  mctx.fillText(TEXT, w / 2, h / 2);

  // ===== COLORS (GitHub style) =====
  const DARK_CELL = "#0d1117";
  const GRID_LINE = "#161b22";
  const GREENS = ["#0e4429", "#006d32", "#26a641", "#39d353"];

  ctx.fillStyle = "#000";
  ctx.fillRect(0, 0, w, h);

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      const x = col * cell + gap / 2;
      const y = row * cell + gap / 2;

      // background grid
      ctx.fillStyle = DARK_CELL;
      ctx.fillRect(x, y, size, size);

      ctx.strokeStyle = GRID_LINE;
      ctx.strokeRect(x, y, size, size);

      // sample text mask
      const sx = Math.floor((col * cell + cell / 2) * dpr);
      const sy = Math.floor((row * cell + cell / 2) * dpr);

      const alpha = mctx.getImageData(sx, sy, 1, 1).data[3];

      // draw text using green commits
      if (alpha > 120) {
        ctx.fillStyle = GREENS[Math.floor(Math.random() * GREENS.length)];
        ctx.fillRect(x, y, size, size);
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
