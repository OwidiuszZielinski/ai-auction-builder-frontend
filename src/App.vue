<script setup>
import { onMounted, onBeforeUnmount, ref } from "vue";

const canvasRef = ref(null);
const text = "Weź się za robotę";

function drawGrid() {
  const c = canvasRef.value;
  if (!c) return;

  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  const w = window.innerWidth;
  const h = window.innerHeight;

  c.width = Math.floor(w * dpr);
  c.height = Math.floor(h * dpr);
  c.style.width = `${w}px`;
  c.style.height = `${h}px`;

  const ctx = c.getContext("2d");
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  ctx.imageSmoothingEnabled = false;

  // ===== rozmiar komórek =====
  const cell = Math.max(8, Math.min(14, Math.floor(Math.min(w, h) / 55)));
  const gap = 2;
  const size = cell - gap;

  const cols = Math.ceil(w / cell);
  const rows = Math.ceil(h / cell);

  // ===== offscreen canvas: tekst =====
  const oc = document.createElement("canvas");
  oc.width = c.width;
  oc.height = c.height;
  const ocCtx = oc.getContext("2d");
  ocCtx.setTransform(dpr, 0, 0, dpr, 0, 0);

  ocCtx.fillStyle = "black";
  ocCtx.fillRect(0, 0, w, h);

  const fontSize = Math.floor(Math.min(w, h) * 0.22);
  ocCtx.fillStyle = "white";
  ocCtx.textAlign = "center";
  ocCtx.textBaseline = "middle";
  ocCtx.font = `900 ${fontSize}px Orbitron, system-ui`;
  ocCtx.fillText(text, w / 2, h / 2);

  // ===== kolory GitHub =====
  const EMPTY_CELL = "#0b0f14";
  const GREENS = ["#0e4429", "#006d32", "#26a641", "#39d353"];

  // ===== tło =====
  ctx.fillStyle = "#000";
  ctx.fillRect(0, 0, w, h);

  // ===== rysowanie siatki =====
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      const x = col * cell + gap / 2;
      const y = row * cell + gap / 2;

      const sampleX = Math.floor((col * cell + cell / 2) * dpr);
      const sampleY = Math.floor((row * cell + cell / 2) * dpr);

      let color = EMPTY_CELL;

      try {
        const [r, g, b, a] = ocCtx.getImageData(sampleX, sampleY, 1, 1).data;

        if (a > 120) {
          // losowa "aktywność" jak w GitHub contributions
          const intensity =
            Math.random() < 0.15 ? 3 :
            Math.random() < 0.35 ? 2 :
            Math.random() < 0.65 ? 1 : 0;

          color = GREENS[intensity];
        }
      } catch {}

      ctx.fillStyle = color;
      ctx.fillRect(x, y, size, size);
    }
  }
}

function onResize() {
  drawGrid();
}

onMounted(() => {
  drawGrid();
  window.addEventListener("resize", onResize);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", onResize);
});
</script>

<template>
  <div class="scene">
    <canvas ref="canvasRef" class="fx" aria-hidden="true"></canvas>

    <!-- Tekst zostaje w DOM tylko dla dostępności -->
    <h1 class="sr-only">Weź się za robotę</h1>

    <footer class="foot">
      Powered with <span class="heart">&lt;3</span> by <span class="sig">Owidiusz</span>
    </footer>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Orbitron:wght@700;900&display=swap");

html, body, #app {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  background: #000;
  overflow: hidden;
}

/* screen-reader only */
.sr-only {
  position: absolute !important;
  width: 1px !important;
  height: 1px !important;
  padding: 0 !important;
  margin: -1px !important;
  overflow: hidden !important;
  clip: rect(0, 0, 0, 0) !important;
  white-space: nowrap !important;
  border: 0 !important;
}
</style>

<style scoped>
.scene {
  position: relative;
  width: 100vw;
  height: 100svh;
  background: #000;
}

.fx {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
}

.foot {
  position: fixed;
  bottom: 14px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 12px;
  color: rgba(255,255,255,0.75);
}

.heart {
  color: #ff5a78;
  animation: beat 1.1s ease-in-out infinite;
}

.sig {
  color: rgba(255,
