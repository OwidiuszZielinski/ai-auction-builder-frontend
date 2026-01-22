<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const canvasRef = ref(null);
const text = "Weź się za robotę";

function draw() {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  const w = window.innerWidth;
  const h = window.innerHeight;

  canvas.width = Math.floor(w * dpr);
  canvas.height = Math.floor(h * dpr);
  canvas.style.width = w + "px";
  canvas.style.height = h + "px";

  const ctx = canvas.getContext("2d");
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  ctx.imageSmoothingEnabled = false;

  // ===== GRID CONFIG =====
  const cell = Math.max(10, Math.floor(Math.min(w, h) / 55));
  const gap = 2;
  const size = cell - gap;

  const cols = Math.ceil(w / cell);
  const rows = Math.ceil(h / cell);

  // ===== OFFSCREEN: TEXT MASK =====
  const off = document.createElement("canvas");
  off.width = canvas.width;
  off.height = canvas.height;
  const octx = off.getContext("2d");
  octx.setTransform(dpr, 0, 0, dpr, 0, 0);

  octx.fillStyle = "black";
  octx.fillRect(0, 0, w, h);

  const fontSize = Math.floor(Math.min(w, h) * 0.22);
  octx.fillStyle = "white";
  octx.textAlign = "center";
  octx.textBaseline = "middle";
  octx.font = `900 ${fontSize}px system-ui, -apple-system, "Segoe UI", Roboto, Arial`;
  octx.fillText(text, w / 2, h / 2);

  // ===== COLORS =====
  const GRID_CELL = "#050505";
  const GRID_LINE = "#111";
  const GREENS = ["#0e4429", "#006d32", "#26a641", "#39d353"];

  // ===== DRAW GRID =====
  ctx.fillStyle = "#000";
  ctx.fillRect(0, 0, w, h);

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      const x = col * cell + gap / 2;
      const y = row * cell + gap / 2;

      // background cell
      ctx.fillStyle = GRID_CELL;
      ctx.fillRect(x, y, size, size);

      // grid lines
      ctx.strokeStyle = GRID_LINE;
      ctx.strokeRect(x, y, size, size);

      // sample text mask
      const sx = Math.floor((col * cell + cell / 2) * dpr);
      const sy = Math.floor((row * cell + cell / 2) * dpr);

      try {
        const alpha = octx.getImageData(sx, sy, 1, 1).data[3];
        if (alpha > 120) {
          const level = Math.floor(Math.random() * GREENS.length);
          ctx.fillStyle = GREENS[level];
          ctx.fillRect(x, y, size, size);
        }
      } catch {}
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
  <div id="app">
    <canvas ref="canvasRef"></canvas>
    <!-- accessibility only -->
    <h1 class="sr-only">Weź się za robotę</h1>
  </div>
</template>

<style>
html, body, #app {
  margin: 0;
  width: 100%;
  height: 100%;
  background: black;
  overflow: hidden;
}

canvas {
  display: block;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
}
</style>
