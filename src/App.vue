<template>
  <div class="app">
    <!-- Confetti -->
    <div class="confetti" v-if="isWinner">
      <span v-for="n in 30" :key="n" :style="confettiStyle(n)"></span>
    </div>

    <!-- Card -->
    <div class="glass-card" :class="{ win: isWinner }">
      <h1>🎮 Wakacyjny Quiz</h1>

      <div class="field">
        <label>gdzie pojedziesz na wakacje</label>
        <input
          v-model="destination"
          type="text"
          placeholder="Wpisz miejsce"
        />
      </div>

      <div class="field">
        <label>jakie bedziesz mial bmw?</label>
        <input
          v-model="bmw"
          type="text"
          placeholder="Wpisz model"
        />
      </div>

      <transition name="pop">
        <div v-if="isWinner" class="winner">
          <div class="banner">🎉 GRATULACJE 🎉</div>
          <p>Wygrałeś gift kartę PS Store</p>

          <div class="code-box">
            <input
              readonly
              value="FBKD-9HKR-KTA9"
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
      bmw: ""
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
      navigator.clipboard.writeText("FBKD-9HKR-KTA9");
    },
    confettiStyle() {
      return {
        left: Math.random() * 100 + "%",
        background: `hsl(${Math.random() * 360}, 80%, 60%)`,
        animationDelay: Math.random() * 2 + "s",
        animationDuration: 2 + Math.random() * 2 + "s"
      };
    }
  }
};
</script>

<style>
/* ===== GLOBAL FIX ===== */
*,
*::before,
*::after {
  box-sizing: border-box;
}

/* ===== Background ===== */
.app {
  min-height: 100vh;
  background: radial-gradient(circle at top, #0f172a, #020617);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 16px;
  font-family: system-ui, -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, Inter, Arial, sans-serif;
  color: #f8fafc;
  overflow: hidden;
}

/* ===== Glass Card ===== */
.glass-card {
  width: 100%;
  max-width: 420px;
  padding: 24px;
  border-radius: 22px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(18px);
  border: 1px solid rgba(255, 255, 255, 0.18);
  box-shadow: 0 30px 60px rgba(0, 0, 0, 0.6);
  transition: all 0.4s ease;
}

.glass-card.win {
  box-shadow:
    0 0 40px rgba(56, 189, 248, 0.6),
    0 0 90px rgba(99, 102, 241, 0.4);
  transform: scale(1.02);
}

/* ===== Title ===== */
h1 {
  text-align: center;
  margin-bottom: 22px;
  font-weight: 800;
  letter-spacing: 0.5px;
}

/* ===== Inputs ===== */
.field {
  width: 100%;
  margin-bottom: 18px;
}

label {
  display: block;
  font-size: 14px;
  font-weight: 600;
  color: #e5e7eb;
}

input {
  width: 100%;
  max-width: 100%;
  margin-top: 6px;
  padding: 14px 16px;
  border-radius: 14px;
  border: none;
  outline: none;
  background: rgba(2, 6, 23, 0.95);
  color: #f9fafb;
  font-size: 16px;
  font-weight: 500;
}

input::placeholder {
  color: #9ca3af;
}

input:focus {
  box-shadow: 0 0 0 2px #38bdf8;
}

/* ===== Winner ===== */
.winner {
  margin-top: 26px;
  text-align: center;
}

.banner {
  background: linear-gradient(90deg, #38bdf8, #6366f1);
  padding: 12px;
  border-radius: 14px;
  font-weight: 800;
  letter-spacing: 1px;
  margin-bottom: 10px;
}

.winner p {
  color: #e5e7eb;
}

/* ===== Code ===== */
.code-box input {
  margin-top: 12px;
  text-align: center;
  letter-spacing: 3px;
  font-weight: 700;
  font-size: 17px;
  cursor: pointer;
}

.code-box span {
  display: block;
  margin-top: 6px;
  font-size: 12px;
  color: #9ca3af;
}

/* ===== Confetti ===== */
.confetti {
  position: absolute;
  inset: 0;
  pointer-events: none;
}

.confetti span {
  position: absolute;
  top: -20px;
  width: 8px;
  height: 14px;
  animation-name: fall;
  animation-timing-function: linear;
  animation-iteration-count: infinite;
}

@keyframes fall {
  to {
    transform: translateY(110vh) rotate(360deg);
  }
}

/* ===== Animation ===== */
.pop-enter-active {
  animation: pop 0.45s ease;
}

@keyframes pop {
  from {
    transform: scale(0.85);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}

/* ===== Mobile tweaks ===== */
@media (max-width: 360px) {
  .glass-card {
    padding: 20px;
  }

  input {
    padding: 12px 14px;
  }
}
</style>
