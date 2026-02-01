<template>
  <div class="app">
    <div class="confetti" v-if="isWinner">
      <span v-for="n in 30" :key="n"></span>
    </div>

    <div class="glass-card" :class="{ win: isWinner }">
      <h1>🎮 Wakacyjny Quiz</h1>

      <div class="field">
        <label>gdzie pojedziesz na wakacje</label>
        <input
          v-model="destination"
          type="text"
          placeholder="np. Hiszpania..."
        />
      </div>

      <div class="field">
        <label>jakie bedziesz mial bmw?</label>
        <input
          v-model="bmw"
          type="text"
          placeholder="np. M3..."
        />
      </div>

      <transition name="pop">
        <div v-if="isWinner" class="winner">
          <div class="banner">🎉 GRATULACJE 🎉</div>
          <p>Wygrałeś gift kartę PS Store</p>

          <div class="code-box">
            <input
              readonly
              :value="giftCode"
              @click="copyCode"
            />
            <span>Kliknij aby skopiować</span>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      destination: "",
      bmw: "",
      giftCode: "TUTAJ_WPROWADZ_SWÓJ_KOD"
    };
  },
  computed: {
    isWinner() {
      return (
        this.destination.trim().toUpperCase() === "MADERA" &&
        this.bmw.trim().toUpperCase() === "M3TOURING"
      );
    }
  },
  methods: {
    copyCode() {
      navigator.clipboard.writeText(this.giftCode);
    }
  }
};
</script>

<style>
/* ===== Background ===== */
.app {
  min-height: 100vh;
  background:
    radial-gradient(circle at top, #1e293b, #020617);
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: "Inter", system-ui, sans-serif;
  padding: 16px;
  overflow: hidden;
}

/* ===== Glass Card ===== */
.glass-card {
  width: 100%;
  max-width: 420px;
  padding: 28px;
  border-radius: 22px;
  background: linear-gradient(
    135deg,
    rgba(255, 255, 255, 0.12),
    rgba(255, 255, 255, 0.04)
  );
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.18);
  box-shadow: 0 30px 60px rgba(0, 0, 0, 0.6);
  transition: box-shadow 0.4s, transform 0.4s;
}

.glass-card.win {
  box-shadow:
    0 0 40px rgba(0, 153, 255, 0.6),
    0 0 80px rgba(0, 153, 255, 0.3);
  transform: scale(1.02);
}

h1 {
  text-align: center;
  margin-bottom: 22px;
  font-weight: 700;
}

/* ===== Inputs ===== */
.field {
  margin-bottom: 18px;
}

label {
  font-size: 13px;
  opacity: 0.85;
}

input {
  width: 100%;
  margin-top: 6px;
  padding: 14px;
  border-radius: 14px;
  border: none;
  outline: none;
  background: rgba(0, 0, 0, 0.45);
  color: #fff;
  font-size: 15px;
}

input:focus {
  box-shadow: 0 0 0 2px #38bdf8;
}

/* ===== Winner ===== */
.winner {
  margin-top: 24px;
  text-align: center;
}

.banner {
  background: linear-gradient(90deg, #38bdf8, #6366f1);
  padding: 10px;
  border-radius: 14px;
  font-weight: 700;
  margin-bottom: 12px;
}

.code-box input {
  margin-top: 10px;
  cursor: pointer;
  text-align: center;
  letter-spacing: 2px;
  font-weight: 600;
}

.code-box span {
  font-size: 12px;
  opacity: 0.6;
}

/* ===== Confetti ===== */
.confetti {
  position: absolute;
  inset: 0;
  pointer-events: none;
}

.confetti span {
  position: absolute;
  width: 8px;
  height: 14px;
  background: hsl(var(--hue), 80%, 60%);
  top: -20px;
  left: calc(100% * var(--x));
  animation: fall 3s linear infinite;
}

.confetti span:nth-child(n) {
  --x: calc(random() * 1);
}

.confetti span {
  --hue: calc(360 * random());
}

@keyframes fall {
  to {
    transform: translateY(110vh) rotate(360deg);
  }
}

/* ===== Animation ===== */
.pop-enter-active {
  animation: pop 0.5s ease;
}

@keyframes pop {
  0% {
    transform: scale(0.8);
    opacity: 0;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

/* ===== Mobile tweaks ===== */
@media (max-width: 480px) {
  h1 {
    font-size: 20px;
  }
}
</style>
