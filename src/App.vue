<template>
  <div class="app">
    <div class="glass-card">
      <h1>🎮 Wakacyjny Quiz</h1>

      <div class="field">
        <label>gdzie pojedziesz na wakacje</label>
        <input
          v-model="destination"
          type="text"
          placeholder="Wpisz miejsce..."
        />
      </div>

      <div class="field">
        <label>jakie bedziesz mial bmw?</label>
        <input
          v-model="bmw"
          type="text"
          placeholder="Wpisz model..."
        />
      </div>

      <transition name="fade">
        <div v-if="isWinner" class="winner">
          <h2>🎉 Gratulacje!</h2>
          <p>Wygrałeś gift kartę PS Store</p>

          <div class="code-box">
            <input
              readonly
              :value="giftCode"
              @click="copyCode"
            />
            <span class="hint">Kliknij aby skopiować</span>
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
/* Background */
.app {
  min-height: 100vh;
  background: radial-gradient(circle at top, #1f2933, #0b0f14);
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: "Inter", sans-serif;
  color: #fff;
}

/* Glass card */
.glass-card {
  width: 420px;
  padding: 32px;
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(18px);
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.15);
}

h1 {
  text-align: center;
  margin-bottom: 24px;
  font-weight: 600;
}

/* Inputs */
.field {
  margin-bottom: 20px;
}

label {
  display: block;
  font-size: 13px;
  margin-bottom: 6px;
  opacity: 0.85;
}

input {
  width: 100%;
  padding: 12px 14px;
  border-radius: 12px;
  border: none;
  outline: none;
  background: rgba(0, 0, 0, 0.35);
  color: #fff;
  font-size: 15px;
  transition: box-shadow 0.2s, background 0.2s;
}

input::placeholder {
  color: rgba(255, 255, 255, 0.4);
}

input:focus {
  background: rgba(0, 0, 0, 0.55);
  box-shadow: 0 0 0 2px rgba(0, 153, 255, 0.4);
}

/* Winner section */
.winner {
  margin-top: 30px;
  text-align: center;
}

.winner h2 {
  margin-bottom: 6px;
}

.winner p {
  opacity: 0.85;
  margin-bottom: 16px;
}

/* Code box */
.code-box input {
  cursor: pointer;
  text-align: center;
  letter-spacing: 2px;
  font-weight: 600;
}

.hint {
  display: block;
  margin-top: 6px;
  font-size: 12px;
  opacity: 0.6;
}

/* Animation */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease, transform 0.4s ease;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(10px);
}
</style>
