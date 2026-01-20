<script setup>
</script>

<template>
  <div class="wrap">
    <h1 class="animated-text">Weź się za robotę</h1>
  </div>
</template>

<style scoped>
.wrap {
  min-height: 100vh;
  display: grid;
  place-items: center;
  padding: 24px;
  background: radial-gradient(circle at 20% 20%, #141b2b, #070a12 60%, #000);
}

.animated-text {
  font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
  font-weight: 900;
  letter-spacing: 0.02em;
  text-align: center;

  /* responsywny rozmiar */
  font-size: clamp(32px, 6vw, 84px);

  /* gradientowy tekst */
  background: linear-gradient(90deg, #7c3aed, #22c55e, #06b6d4, #7c3aed);
  background-size: 300% 100%;
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;

  /* animacje */
  animation:
    gradientMove 2.5s linear infinite,
    pulse 1.2s ease-in-out infinite,
    tinyShake 3.5s ease-in-out infinite;
  filter: drop-shadow(0 10px 30px rgba(124, 58, 237, 0.25));
}

/* przesuwający się gradient */
@keyframes gradientMove {
  0%   { background-position: 0% 50%; }
  100% { background-position: 100% 50%; }
}

/* delikatne pompowanie */
@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50%      { transform: scale(1.04); }
}

/* mikro-shake co jakiś czas */
@keyframes tinyShake {
  0%, 88%, 100% { transform: translateX(0) scale(1); }
  90% { transform: translateX(-2px) scale(1.01); }
  92% { transform: translateX(2px)  scale(1.01); }
  94% { transform: translateX(-2px) scale(1.01); }
  96% { transform: translateX(2px)  scale(1.01); }
}
</style>
