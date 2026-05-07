<template>
  <div id="app">
    <div v-if="showSplash" class="splash">
      <h1>Whack-a-Mole</h1>
      <p>Welcome to the Whack-a-Mole game!</p>
      <p>Instructions:</p>
      <ul>
        <li>A mole will appear in one of the 9 holes every second.</li>
        <li>Click on the mole to score 10 points.</li>
        <li>If you miss, the mole will hide after 1 second.</li>
        <li>Try to get the highest score!</li>
      </ul>
      <button @click="startGame">Start Game</button>
    </div>
    <div v-else class="game">
      <h1>Whack-a-Mole</h1>
      <div class="score">Score: {{ score }}</div>
      <button @click="stopGame" :disabled="!gameRunning">Stop Game</button>
      <button @click="resetGame">Back to Menu</button>
      <div class="board">
        <div
          v-for="hole in holes"
          :key="hole.index"
          class="hole"
          @click="hitMole(hole.index)"
        >
          <div v-if="activeMole === hole.index" class="mole">🐼</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'WhackAMole',
  data() {
    return {
      activeMole: null,
      score: 0,
      gameRunning: false,
      gameInterval: null,
      timeoutId: null,
      showSplash: true,
      holes: Array.from({ length: 9 }, (_, i) => ({ index: i }))
    }
  },
  methods: {
    startGame() {
      this.showSplash = false
      this.gameRunning = true
      this.score = 0
      this.activeMole = null
      this.gameInterval = setInterval(this.showMole, 1000)
    },
    stopGame() {
      this.gameRunning = false
      if (this.gameInterval) {
        clearInterval(this.gameInterval)
        this.gameInterval = null
      }
      if (this.timeoutId) {
        clearTimeout(this.timeoutId)
        this.timeoutId = null
      }
      this.activeMole = null
    },
    showMole() {
      if (this.timeoutId) {
        clearTimeout(this.timeoutId)
      }
      const randomHole = Math.floor(Math.random() * 9)
      this.activeMole = randomHole
      this.timeoutId = setTimeout(() => {
        this.activeMole = null
        this.timeoutId = null
      }, 1000)
    },
    resetGame() {
      this.stopGame()
      this.showSplash = true
      this.score = 0
    },
    hitMole(index) {
      if (this.activeMole === index && this.gameRunning) {
        this.score += 10
        this.activeMole = null
        if (this.timeoutId) {
          clearTimeout(this.timeoutId)
          this.timeoutId = null
        }
      }
    }
  },
  beforeDestroy() {
    this.stopGame()
  }
}
</script>

<style scoped>
#app {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  text-align: center;
  color: #fff;
  margin: 0;
  padding: 0;
  background: linear-gradient(135deg, #180b08 0%, #2b1810 50%, #3b2418 100%);
  min-height: 100vh;
  height: 100vh;
  width: 100%;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  overflow-x: hidden;
  overflow-y: auto;
}

.splash {
  background: linear-gradient(135deg, #4E342E 0%, #5D4037 100%);
  border-radius: 25px;
  padding: 45px;
  box-shadow: 0 25px 60px rgba(0, 0, 0, 0.9), 0 0 40px rgba(210, 180, 140, 0.3);
  max-width: 500px;
  animation: fadeIn 1s ease-in;
  border: 3px solid #D2B48C;
  backdrop-filter: blur(10px);
}

.splash h1 {
  color: #D2B48C;
  font-size: 3em;
  margin-bottom: 10px;
  text-shadow: 0 0 20px rgba(210, 180, 140, 0.5), 2px 2px 4px rgba(0, 0, 0, 0.7);
  font-weight: 700;
}

.splash p {
  font-size: 1.1em;
  margin: 8px 0;
  color: #e0e0e0;
  font-weight: 300;
  letter-spacing: 0.5px;
}

.splash ul {
  text-align: left;
  margin: 20px 0;
  padding-left: 25px;
  list-style: none;
}

.splash li {
  margin: 12px 0;
  font-size: 1em;
  color: #b0b0b0;
  padding-left: 25px;
  position: relative;
}

.splash li:before {
  content: "▸";
  position: absolute;
  left: 0;
  color: #D2B48C;
  font-weight: bold;
}

.splash button {
  background: linear-gradient(135deg, #D2B48C 0%, #CD853F 100%);
  color: #2C1810;
  border: none;
  padding: 16px 45px;
  font-size: 1.1em;
  font-weight: 700;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s;
  box-shadow: 0 10px 30px rgba(210, 180, 140, 0.4), 0 0 20px rgba(210, 180, 140, 0.2);
  letter-spacing: 1px;
  margin-top: 20px;
}

.splash button:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 40px rgba(210, 180, 140, 0.6), 0 0 30px rgba(210, 180, 140, 0.3);
}

.game {
  background: linear-gradient(135deg, #4E342E 0%, #5D4037 100%);
  border-radius: 25px;
  padding: 25px;
  box-shadow: 0 25px 60px rgba(0, 0, 0, 0.9), 0 0 40px rgba(210, 180, 140, 0.2);
  width: 90vw;
  max-width: 600px;
  animation: slideIn 0.5s ease-in;
  border: 3px solid #D2B48C;
  backdrop-filter: blur(10px);
  box-sizing: border-box;
}

h1 {
  color: #e94560;
  font-size: 2.5em;
  margin-bottom: 20px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.score {
  font-size: 1.8em;
  margin: 12px 0;
  color: #D2B48C;
  font-weight: 700;
  text-shadow: 0 0 15px rgba(210, 180, 140, 0.5), 2px 2px 4px rgba(0, 0, 0, 0.7);
  letter-spacing: 1px;
}

button {
  background: linear-gradient(135deg, #D2B48C 0%, #CD853F 100%);
  color: #2C1810;
  border: none;
  padding: 10px 22px;
  margin: 6px;
  font-size: 0.95em;
  font-weight: 700;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 8px 20px rgba(210, 180, 140, 0.4), 0 0 15px rgba(210, 180, 140, 0.2);
  letter-spacing: 0.5px;
}

button:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 12px 30px rgba(210, 180, 140, 0.6), 0 0 20px rgba(210, 180, 140, 0.3);
}

button:disabled {
  background: linear-gradient(135deg, #5D4037 0%, #3E2723 100%);
  cursor: not-allowed;
  box-shadow: none;
  color: #999;
}

.board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 14px;
  width: 100%;
  margin: 18px auto;
  padding: 25px;
  background: linear-gradient(135deg, #6D4C41 0%, #795548 100%);
  border-radius: 20px;
  box-shadow: inset 0 0 40px rgba(0, 0, 0, 0.9), inset 0 0 20px rgba(210, 180, 140, 0.1);
  border: 2px solid #D2B48C;
  box-sizing: border-box;
}

.hole {
  aspect-ratio: 1;
  min-width: 50px;
  background: radial-gradient(circle at 35% 35%, #6d4c41 0%, #2e1b13 100%);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border: 5px solid #392018;
  position: relative;
  overflow: hidden;
  box-shadow: inset 0 0 25px rgba(0, 0, 0, 0.98), inset 0 3px 10px rgba(140, 105, 70, 0.12), 0 8px 20px rgba(0, 0, 0, 0.75);
  transition: all 0.15s;
}

.hole:hover {
  transform: scale(1.08);
  box-shadow: inset 0 0 25px rgba(0, 0, 0, 0.98), inset 0 3px 10px rgba(140, 105, 70, 0.18), 0 10px 25px rgba(140, 105, 70, 0.25);
}

.mole {
  font-size: 55px;
  animation: popUp 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
  filter: drop-shadow(0 0 8px rgba(210, 180, 140, 0.4));
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-40px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes slideIn {
  from { opacity: 0; transform: scale(0.85) translateY(20px); }
  to { opacity: 1; transform: scale(1) translateY(0); }
}

@keyframes popUp {
  0% { transform: scale(0) rotate(-180deg) translateY(20px); }
  50% { transform: scale(1.35) rotate(10deg); }
  100% { transform: scale(1) rotate(0deg) translateY(0); }
}

@media (max-width: 768px) {
  .game {
    width: 95%;
    padding: 20px;
  }
  h1 {
    font-size: 2em;
  }
  .board {
    max-width: 350px;
    gap: 10px;
    padding: 15px;
  }
  .hole {
    width: 80px;
    height: 80px;
  }
  .mole {
    font-size: 30px;
  }
}

@media (max-width: 480px) {
  .game {
    width: 98%;
    padding: 15px;
  }
  h1 {
    font-size: 1.8em;
  }
  .score {
    font-size: 1.5em;
  }
  .board {
    max-width: 300px;
    gap: 8px;
    padding: 12px;
  }
  .hole {
    width: 70px;
    height: 70px;
  }
  .mole {
    font-size: 25px;
  }
  button {
    padding: 10px 15px;
    font-size: 0.9em;
    margin: 5px;
  }
}
</style>

