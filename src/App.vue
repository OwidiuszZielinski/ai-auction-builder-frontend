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
  w = c.clientWidth;
  h = c.clientHeight;

  c.width = Math.floor(w * dpr);
  c.height = Math.floor(h * dpr);

  const ctx = c.getContext("2d");
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);

  // zainicjuj cząstki na nowo przy zmianie rozmiaru
  const count = Math.floor((w * h) / 18000);
  particles = Array.from({ length: count }, () => ({
    x: rand(0, w),
    y: rand(0, h),
    vx: rand(-0.25, 0.25),
    vy: rand(-0.35, 0.35),
    r: rand(0.6, 1.8),
    a: rand(0.08, 0.35),
    hue: rand(170, 280),
  }));
}

function draw() {
  const c = canvasRef.value;
  if (!c) return;
  const ctx = c.getContext("2d");

  t += 0.016;

  // fade tła
  ctx.clearRect(0, 0, w, h);

  // miękki „mgielny” glow
  const grd = ctx.createRadialGradient(w * 0.25, h * 0.35, 10, w * 0.25, h * 0.35, Math.max(w, h));
  grd.addColorStop(0, "rgba(124,58,237,0.18)");
  grd.addColorStop(0.5, "rgba(34,197,94,0.06)");
  grd.addColorStop(1, "rgba(0,0,0,0)");
  ctx.fillStyle = grd;
  ctx.fillRect(0, 0, w, h);

  // cząstki + połączenia
  for (let i = 0; i < particles.length; i++) {
    const p = particles[i];

    p.x += p.vx;
    p.y += p.vy;

    // odbijanie od krawędzi
    if (p.x < -20) p.x = w + 20;
    if (p.x > w + 20) p.x = -20;
    if (p.y < -20) p.y = h + 20;
    if (p.y > h + 20) p.y = -20;

    // migotanie alpha
    const twinkle = 0.08 * Math.sin(t * rand(1.5, 3.0) + i);
    const alpha = Math.max(0.02, Math.min(0.55, p.a + twinkle));

    // punkt
    ctx.beginPath();
    ctx.fillStyle = `hsla(${p.hue}, 95%, 70%, ${alpha})`;
    ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
    ctx.fill();

    // połączenia (subtelne)
    for (let j = i + 1; j < particles.length; j++) {
      const q = particles[j];
      const dx = p.x - q.x;
      const dy = p.y - q.y;
      const dist = Math.hypot(dx, dy);
      if (dist < 120) {
        const a2 = (1 - dist / 120) * 0.10;
        ctx.strokeStyle = `hsla(${(p.hue + q.hue) / 2}, 95%, 70%, ${a2})`;
        ctx.lineWidth = 1;
        ctx.beginPath();
        ctx.moveTo(p.x, p.y);
        ctx.lineTo(q.x, q.y);
        ctx.stroke();
      }
    }
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

    <div class="content">
      <!-- „szyba” / glass -->
      <div class="panel">
        <div class="badge">
          <span class="dot"></span>
          Tryb produktywności
        </div>

        <!-- Neon + glitch + typewriter -->
        <h1 class="headline" aria-label="Weź się za robotę">
          <span class="glitch" data-text="Weź się za robotę">Weź się za robotę</span>
        </h1>

        <p class="sub">
          Zrób najmniejszy możliwy krok. <span class="hint">Teraz.</span>
        </p>

        <div class="actions">
          <button class="btn primary" @click="() => window.scrollTo({ top: 0, behavior: 'smooth' })">
            Start
          </button>
          <button class="btn ghost" @click="() => location.reload()">
            Reset animacji
          </button>
        </div>

        <div class="meter">
          <div class="meter-label">
            <span>Momentum</span>
            <span class="value">99%</span>
          </div>
          <div class="bar">
            <div class="fill"></div>
          </div>
        </div>
      </div>

      <div class="foot">
    Powered with <span class="heart" aria-hidden="true">&lt;3</span> by <span class="sig">Owidiusz</span>
    </div>
    </div>
  </div>
</template>

<style scoped>
/* --- tło / layout --- */
.scene {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
  background: radial-gradient(circle at 20% 20%, #121a2e, #050610 60%, #000);
  display: grid;
  place-items: center;
  padding: 28px;
}

.fx {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  filter: saturate(1.15) contrast(1.05);
  opacity: 0.95;
}

/* --- content --- */
.content {
  position: relative;
  z-index: 2;
  width: min(920px, 100%);
  display: grid;
  gap: 18px;
  place-items: center;
}

.panel {
  width: 100%;
  padding: clamp(18px, 3.2vw, 34px);
  border-radius: 22px;
  background: linear-gradient(180deg, rgba(255,255,255,0.08), rgba(255,255,255,0.03));
  border: 1px solid rgba(255,255,255,0.14);
  box-shadow:
    0 20px 60px rgba(0,0,0,0.55),
    inset 0 1px 0 rgba(255,255,255,0.12);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}

/* badge */
.badge {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  font: 600 14px/1 system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
  color: rgba(255,255,255,0.82);
  padding: 10px 14px;
  border-radius: 999px;
  background: rgba(0,0,0,0.25);
  border: 1px solid rgba(255,255,255,0.10);
}

.dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: radial-gradient(circle, #22c55e, #16a34a);
  box-shadow: 0 0 18px rgba(34,197,94,0.65);
  animation: dotPulse 1.2s ease-in-out infinite;
}

@keyframes dotPulse {
  0%,100% { transform: scale(1); filter: brightness(1); }
  50% { transform: scale(1.25); filter: brightness(1.3); }
}

/* --- headline: neon + typewriter + glitch --- */
.headline {
  margin: 18px 0 10px;
  text-align: center;
  font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
  font-weight: 900;
  letter-spacing: -0.02em;
  font-size: clamp(34px, 6vw, 86px);
  line-height: 1.02;
}

/* typewriter mask */
.glitch {
  position: relative;
  display: inline-block;
  color: transparent;
  background: linear-gradient(90deg, #7c3aed, #22c55e, #06b6d4, #7c3aed);
  background-size: 260% 100%;
  -webkit-background-clip: text;
  background-clip: text;

  /* neon glow */
  text-shadow:
    0 0 10px rgba(124,58,237,0.45),
    0 0 22px rgba(34,197,94,0.22),
    0 18px 48px rgba(0,0,0,0.65);

  /* animacje: gradient + typewriter */
  animation:
    gradientMove 2.6s linear infinite,
    typeReveal 1.8s steps(18, end) 0.15s forwards,
    caretBlink 0.75s step-end infinite;
  overflow: hidden;
  white-space: nowrap;
  border-right: 3px solid rgba(255,255,255,0.85);
  width: 0; /* start dla typewriter */
}

/* pseudo-elementy do glitch */
.glitch::before,
.glitch::after {
  content: attr(data-text);
  position: absolute;
  inset: 0;
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  background: inherit;
  opacity: 0.85;
  pointer-events: none;
  mix-blend-mode: screen;
}

.glitch::before {
  transform: translate(-2px, 0);
  filter: hue-rotate(35deg);
  animation: glitchTop 2.6s infinite;
  clip-path: inset(0 0 55% 0);
}

.glitch::after {
  transform: translate(2px, 0);
  filter: hue-rotate(-35deg);
  animation: glitchBot 3.1s infinite;
  clip-path: inset(55% 0 0 0);
}

@keyframes gradientMove {
  0%   { background-position: 0% 50%; }
  100% { background-position: 100% 50%; }
}

@keyframes typeReveal {
  from { width: 0; }
  to   { width: 100%; }
}

@keyframes caretBlink {
  50% { border-right-color: transparent; }
}

@keyframes glitchTop {
  0%, 82%, 100% { transform: translate(-2px, 0); }
  84% { transform: translate(-6px, -1px) skewX(-10deg); }
  86% { transform: translate(2px, 1px) skewX(8deg); }
  88% { transform: translate(-4px, 0) skewX(-6deg); }
}

@keyframes glitchBot {
  0%, 78%, 100% { transform: translate(2px, 0); }
  80% { transform: translate(6px, 1px) skewX(10deg); }
  82% { transform: translate(-2px, -1px) skewX(-8deg); }
  84% { transform: translate(4px, 0) skewX(6deg); }
}

/* --- podtytuł --- */
.sub {
  margin: 0;
  text-align: center;
  color: rgba(255,255,255,0.78);
  font: 500 clamp(14px, 1.4vw, 18px)/1.6 system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
}

.hint {
  color: rgba(255,255,255,0.92);
  text-shadow: 0 0 14px rgba(6,182,212,0.25);
}

/* --- przyciski --- */
.actions {
  margin-top: 18px;
  display: flex;
  gap: 10px;
  justify-content: center;
  flex-wrap: wrap;
}

.btn {
  border: 1px solid rgba(255,255,255,0.14);
  border-radius: 14px;
  padding: 12px 16px;
  font: 700 14px/1 system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
  cursor: pointer;
  transition: transform 0.12s ease, filter 0.12s ease, background 0.12s ease;
  user-select: none;
}

.btn:active { transform: translateY(1px) scale(0.99); }

.primary {
  color: rgba(255,255,255,0.95);
  background: linear-gradient(90deg, rgba(124,58,237,0.85), rgba(34,197,94,0.75));
  box-shadow: 0 10px 30px rgba(124,58,237,0.22);
}

.primary:hover { filter: brightness(1.12); }

.ghost {
  color: rgba(255,255,255,0.85);
  background: rgba(0,0,0,0.22);
}

.ghost:hover { filter: brightness(1.12); }

/* --- „momentum bar” --- */
.meter {
  margin-top: 18px;
  display: grid;
  gap: 10px;
}

.meter-label {
  display: flex;
  justify-content: space-between;
  color: rgba(255,255,255,0.75);
  font: 600 13px/1 system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
}

.value {
  color: rgba(255,255,255,0.92);
}

.bar {
  height: 10px;
  border-radius: 999px;
  background: rgba(255,255,255,0.08);
  border: 1px solid rgba(255,255,255,0.10);
  overflow: hidden;
}

.fill {
  height: 100%;
  width: 0%;
  border-radius: 999px;
  background: linear-gradient(90deg, rgba(34,197,94,0.9), rgba(6,182,212,0.9), rgba(124,58,237,0.9));
  animation: fillUp 1.8s ease forwards;
  box-shadow: 0 0 18px rgba(34,197,94,0.22);
}

@keyframes fillUp {
  to { width: 99%; }
}

/* stopka */
.foot {
  color: rgba(255,255,255,0.55);
  font: 600 12px/1 system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
  text-align: center;
}

.kbd {
  display: inline-block;
  padding: 3px 8px;
  border-radius: 8px;
  background: rgba(255,255,255,0.06);
  border: 1px solid rgba(255,255,255,0.10);
  color: rgba(255,255,255,0.70);
  margin: 0 2px;
}
  .heart {
  display: inline-block;
  margin: 0 6px;
  color: rgba(255, 90, 120, 0.95);
  text-shadow: 0 0 14px rgba(255, 90, 120, 0.35);
  animation: heartBeat 1.1s ease-in-out infinite;
}

.sig {
  color: rgba(255,255,255,0.82);
  text-shadow: 0 0 14px rgba(6, 182, 212, 0.18);
}

@keyframes heartBeat {
  0%, 100% { transform: scale(1); }
  45% { transform: scale(1.18); }
  70% { transform: scale(1.05); }
}

</style>
