<script setup>
import { onMounted, onBeforeUnmount, ref } from "vue";

const canvasRef = ref(null);

let rafId = 0;
let particles = [];
let w = 0;
let h = 0;
let t = 0;

function rand(min, max) {
  return Math.random() * (max - min) + min;
}

function resize() {
  const c = canvasRef.value;
  if (!c) return;

  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  w = window.innerWidth;
  h = window.innerHeight;

  c.width = Math.floor(w * dpr);
  c.height = Math.floor(h * dpr);

  const ctx = c.getContext("2d");
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);

  const count = Math.floor((w * h) / 22000);
  particles = Array.from({ length: count }, () => ({
    x: rand(0, w),
    y: rand(0, h),
    vx: rand(-0.2, 0.2),
    vy: rand(-0.3, 0.3),
    r: rand(0.6, 1.6),
    a: rand(0.08, 0.35),
    hue: rand(170, 280),
  }));
}

function draw() {
  const c = canvasRef.value;
  if (!c) return;
  const ctx = c.getContext("2d");

  t += 0.016;
  ctx.clearRect(0, 0, w, h);

  const grd = ctx.createRadialGradient(
    w * 0.3,
    h * 0.35,
    10,
    w * 0.3,
    h * 0.35,
    Math.max(w, h)
  );
  grd.addColorStop(0, "rgba(124,58,237,0.18)");
  grd.addColorStop(0.5, "rgba(34,197,94,0.06)");
  grd.addColorStop(1, "rgba(0,0,0,0)");
  ctx.fillStyle = grd;
  ctx.fillRect(0, 0, w, h);

  for (let i = 0; i < particles.length; i++) {
    const p = particles[i];
    p.x += p.vx;
    p.y += p.vy;

    if (p.x < -20) p.x = w + 20;
    if (p.x > w + 20) p.x = -20;
    if (p.y < -20) p.y = h + 20;
    if (p.y > h + 20) p.y = -20;

    const twinkle = 0.08 * Math.sin(t * 2 + i);
    const alpha = Math.max(0.05, Math.min(0.5, p.a + twinkle));

    ctx.beginPath();
    ctx.fillStyle = `hsla(${p.hue}, 95%, 70%, ${alpha})`;
    ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
    ctx.fill();
  }

  rafId = requestAnimationFrame(draw);
}

onMounted(() => {
  resize();
  window.addEventListener("resize", resize);
  rafId = requestAnimationFrame(draw);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", resize);
  cancelAnimationFrame(rafId);
});
</script>

<template>
  <div class="scene">
    <canvas ref="canvasRef" class="fx" aria-hidden="true"></canvas>

    <main class="wrap">
      <section class="panel">
        <h1 class="headline">
          <span class="glitch" data-text="Weź się za robotę">
            Weź się za robotę
          </span>
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
</style>

<style scoped>
.scene {
  position: relative;
  width: 100vw;
  height: 100svh;
  display: grid;
  place-items: center;
  background: radial-gradient(circle at 20% 20%, #121a2e, #050610 60%, #000);
}

.fx {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
}

/* centralny wrapper – KLUCZ do dopasowania */
.wrap {
  position: relative;
  z-index: 2;
  width: min(100%, 960px);
  padding: clamp(16px, 4vw, 48px);
  display: grid;
  gap: 20px;
  align-items: center;
  justify-items: center;
}

/* panel */
.panel {
  width: 100%;
  padding: clamp(20px, 4vw, 40px);
  border-radius: 22px;
  background: linear-gradient(180deg, rgba(255,255,255,0.08), rgba(255,255,255,0.03));
  border: 1px solid rgba(255,255,255,0.14);
  backdrop-filter: blur(14px);
  box-shadow: 0 30px 80px rgba(0,0,0,0.6);
  text-align: center;
}

/* headline */
.headline {
  margin: 0;
  font-family: "Orbitron", system-ui, sans-serif;
  font-weight: 900;
  line-height: 1.05;
  font-size: clamp(32px, 6vw, 88px);
  letter-spacing: 0.08em;
}

/* glitch + neon */
.glitch {
  position: relative;
  display: inline-block;
  white-space: nowrap;
  overflow: hidden;
  width: 0;
  color: transparent;
  background: linear-gradient(90deg, #7c3aed, #22c55e, #06b6d4, #7c3aed);
  background-size: 300% 100%;
  -webkit-background-clip: text;
  background-clip: text;
  text-shadow:
    0 0 14px rgba(124,58,237,0.6),
    0 0 28px rgba(34,197,94,0.35);
  animation:
    reveal 1.6s steps(18, end) forwards,
    gradient 3s linear infinite;
}

.glitch::after {
  content: "";
  position: absolute;
  right: -6px;
  top: 10%;
  width: 3px;
  height: 80%;
  background: linear-gradient(180deg, #22c55e, #06b6d4, #7c3aed);
  box-shadow:
    0 0 12px rgba(34,197,94,0.9),
    0 0 22px rgba(124,58,237,0.7);
  animation: caret 0.9s steps(1) infinite;
}

@keyframes reveal {
  to { width: 100%; }
}

@keyframes gradient {
  to { background-position: 100% 50%; }
}

@keyframes caret {
  50% { opacity: 0; }
}

/* tekst */
.sub {
  margin-top: 16px;
  font-size: clamp(14px, 2.5vw, 18px);
  color: rgba(255,255,255,0.78);
}

.hint {
  color: #fff;
  text-shadow: 0 0 12px rgba(6,182,212,0.35);
}

/* stopka */
.foot {
  font-size: 12px;
  color: rgba(255,255,255,0.55);
  text-align: center;
}

.heart {
  color: #ff5a78;
  text-shadow: 0 0 14px rgba(255,90,120,0.5);
  animation: beat 1.1s ease-in-out infinite;
}

.sig {
  color: rgba(255,255,255,0.85);
}

@keyframes beat {
  50% { transform: scale(1.15); }
}
</style>
