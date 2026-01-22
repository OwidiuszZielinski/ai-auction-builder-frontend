<script setup>
import { onMounted, onBeforeUnmount, ref } from "vue";

const canvasRef = ref(null);
let rafId = 0;

const text = "Weź się za robotę";

function drawGrid() {
  const c = canvasRef.value;
  if (!c) return;

  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  const w = window.innerWidth;
  const h = window.innerHeight;

  // ustawienia canvas
  c.width = Math.floor(w * dpr);
  c.height = Math.floor(h * dpr);
  c.style.width = `${w}px`;
  c.style.height = `${h}px`;

  const ctx = c.getContext("2d");
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);

  // dobierz rozmiar pojedynczego "pola" (mały kwadracik)
  const baseCell = Math.max(6, Math.min(14, Math.floor(Math.min(w, h) / 60)));
  const cell = baseCell;
  const cols = Math.ceil(w / cell);
  const rows = Math.ceil(h / cell);
  const gap = Math.max(1, Math.floor(cell * 0.12));
  const boxSize = cell - gap;

  // offscreen canvas: renderujemy tekst i próbkowujemy jego piksele
  const oc = document.createElement("canvas");
  oc.width = c.width;
  oc.height = c.height;
  const ocCtx = oc.getContext("2d");
  ocCtx.setTransform(dpr, 0, 0, dpr, 0, 0);

  // tle offscreen i tekst w bieli
  ocCtx.clearRect(0, 0, w, h);
  ocCtx.fillStyle = "black";
  ocCtx.fillRect(0, 0, w, h);

  // dopasuj wielkość fontu do ekranu
  const fontSize = Math.floor(Math.min(w, h) * 0.18);
  ocCtx.fillStyle = "white";
  ocCtx.textAlign = "center";
  ocCtx.textBaseline = "middle";
  ocCtx.font = `700 ${fontSize}px "Orbitron", system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial`;
  ocCtx.fillText(text, w / 2, h / 2);

  // tło główne
  ctx.fillStyle = "#02030a";
  ctx.fillRect(0, 0, w, h);

  // opcjonalny miękki gradient tła po lewej (dla klimatu)
  const g = ctx.createRadialGradient(w * 0.25, h * 0.35, 10, w * 0.25, h * 0.35, Math.max(w, h));
  g.addColorStop(0, "rgba(28,37,57,0.18)");
  g.addColorStop(0.6, "rgba(6,12,23,0.05)");
  g.addColorStop(1, "rgba(0,0,0,0)");
  ctx.fillStyle = g;
  ctx.fillRect(0, 0, w, h);

  // rysuj siatkę; próbkuj środek każdego pola z offscreen canvas
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      const x = col * cell + gap / 2;
      const y = row * cell + gap / 2;

      // pozycja do sample'owania (w pixelach kanwy) — używamy środków pól
      const sampleX = Math.floor((col * cell + cell / 2) * dpr);
      const sampleY = Math.floor((row * cell + cell / 2) * dpr);

      // domyślny kolor pola (ciemne)
      ctx.fillStyle = "#071021";
      ctx.fillRect(x, y, boxSize, boxSize);

      // sprawdź alfa piksela z tekstu
      let isFilled = false;
      try {
        const pixel = ocCtx.getImageData(sampleX, sampleY, 1, 1).data;
        // jeśli biały (lub półprzezroczysty), traktuj jako część znaku
        if (pixel[3] > 110) {
          isFilled = true;
        } else if (pixel[0] + pixel[1] + pixel[2] > 200) {
          isFilled = true;
        }
      } catch (e) {
        // w rzadkich przypadkach getImageData może rzucić; ignorujemy wtedy
        isFilled = false;
      }

      if (isFilled) {
        // wypełniony zielony kwadrat — styl podobny do "contribution" GitHub
        ctx.fillStyle = "#16a34a";
        ctx.fillRect(x, y, boxSize, boxSize);

        // lekki blask/gradient dla lepszego efektu
        ctx.fillStyle = "rgba(255,255,255,0.03)";
        ctx.fillRect(x, y, boxSize, boxSize * 0.18);
      }
    }
  }
}

function onResize() {
  // przerysuj po zmianie rozmiaru
  drawGrid();
}

onMounted(() => {
  drawGrid();
  window.addEventListener("resize", onResize);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", onResize);
  cancelAnimationFrame(rafId);
});
</script>

<template>
  <div class="scene">
    <canvas ref="canvasRef" class="fx" aria-hidden="true"></canvas>

    <main class="wrap">
      <section class="panel">
        <!-- Tekst zostaje w DOM dla dostępności, ale jest ukryty wizualnie.
             Graficzną reprezentację tworzy canvas powyżej. -->
        <h1 class="headline sr-only">
          <span class="glitch" data-text="Weź się za robotę">Weź się za robotę</span>
        </h1>
      </section>

      <footer class="foot">
        Powered with <span class="heart">&lt;3</span> by <span class="sig">Owidiusz</span>
      </footer>
    </main>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Orbitron:wght@600;800;900&display=swap");

/* globalny reset */
html, body, #app {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  background: #000;
  overflow: hidden;
}

/* accessibility helper: visually hide but keep for screen readers */
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
  display: block;
  background: #000;
}

/* canvas zajmuje cały widok i jest tłem typu "siatka" */
.fx {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
  display: block;
  z-index: 0;
}

/* centralny wrapper – elementy interfejsu na wierzchu */
.wrap {
  position: relative;
  z-index: 2;
  width: min(100%, 960px);
  padding: clamp(12px, 3.5vw, 36px);
  display: grid;
  gap: 12px;
  align-items: center;
  justify-items: center;
}

/* panel - półprzezroczysty kontener nad kanwą */
.panel {
  width: 100%;
  padding: clamp(12px, 3vw, 28px);
  border-radius: 16px;
  background: linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0.01));
  border: 1px solid rgba(255,255,255,0.06);
  backdrop-filter: blur(6px);
  box-shadow: 0 18px 50px rgba(0,0,0,0.6);
  text-align: center;
}

/* stopka */
.foot {
  font-size: 12px;
  color: rgba(255,255,255,0.75);
  text-align: center;
  z-index: 3;
  display: block;
}

.heart {
  color: #ff5a78;
  text-shadow: 0 0 8px rgba(255,90,120,0.4);
  animation: beat 1.1s ease-in-out infinite;
}

.sig {
  color: rgba(255,255,255,0.92);
  font-weight: 600;
}

@keyframes beat {
  50% { transform: scale(1.12); }
}
</style>
