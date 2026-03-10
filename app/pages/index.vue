<script setup>
import { ref } from "vue";
import MathGame from "~/components/MathGame.vue";

const chosenMathOperation = ref(null);
const chosenDifficulty = ref(null);
const showStartMessage = ref(false);
const gameStarted = ref(false);

const operationNames = {
  addition: 'sčítání',
  subtraction: 'odčítání',
  multiplication: 'násobení',
  division: 'dělení'
}

const difficultyNames = {
  easy: 'lehká',
  medium: 'střední',
  hard: 'těžká'
}

const resetToHome = () => {
  gameStarted.value = false;
  chosenMathOperation.value = null;
  chosenDifficulty.value = null;
  showStartMessage.value = false;
}
</script>

<template>
  <div v-if="!gameStarted" class="app-container">
    <link href="https://fonts.googleapis.com/css2?family=Hanalei+Fill&family=Indie+Flower&family=Mansalva&display=swap" rel="stylesheet"/>
    <div class="header">
      <h1>Pojďme si společně procvičit počítání</h1>
    </div>
    <div class="controls">
      <div v-if="!chosenMathOperation" class="math-operation">
          <h2>Co si dnes zkusíme?</h2>
        <button :style="{ animationDelay: '0.1s'}" @click="chosenMathOperation = 'addition'">Sčítání</button>
        <button :style="{ animationDelay: '0.3s'}" @click="chosenMathOperation = 'subtraction'">Odčítání</button>
        <button :style="{ animationDelay: '0.4s'}" @click="chosenMathOperation = 'multiplication'">Násobení</button>
        <button :style="{ animationDelay: '0.5s'}" @click="chosenMathOperation = 'division'">Dělení</button>
      </div>
      <div v-else-if="!chosenDifficulty" class="difficulty-selection">
          <h2>Vyber obtížnost</h2>
        <button :style="{ animationDelay: '0.1s'}" @click="chosenDifficulty = 'easy'; showStartMessage = true">Lehká 🌱</button>
        <button :style="{ animationDelay: '0.3s'}" @click="chosenDifficulty = 'medium'; showStartMessage = true">Střední 💡</button>
        <button :style="{ animationDelay: '0.4s'}" @click="chosenDifficulty = 'hard'; showStartMessage = true">Těžká 🚀</button>
      </div>
      <div v-else-if="showStartMessage" class="welcome-screen">
        <h2>Máš vybráno {{ operationNames[chosenMathOperation] }} a obtížnost {{ difficultyNames[chosenDifficulty] }}</h2>
        <button @click="showStartMessage = false; gameStarted = true">Jdeme na to! 🧠</button>
      </div>
    </div>
</div>
  <MathGame v-else 
  :operation="chosenMathOperation"
  :difficulty="chosenDifficulty"
  @close="resetToHome"/>
</template>

<style scoped>
@keyframes popInDown {
  0% {
    opacity: 0;
    transform: translateY(-50px) scale(0.9);
  }
  100% {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.math-operation button, 
.difficulty-selection button {
  animation: popInDown 0.9s ease-out backwards;
}
.app-container {
  background-image: url("/pics/tech-friend.jpg");
  background-size: cover;
  min-height: 100vh;
  font-family: "Indie Flower";
}

.header h1 {
  text-align: center;
  font-size: 50px;
  color: rgb(255, 242, 0);
  margin-top: 0;
}

.controls {
  justify-content: center;
  text-align: center;
  font-size: 20px;
  color: rgb(253, 255, 156);
  gap: 20px;
  margin-top: 50px;
}

button {
  background-color: rgb(255, 242, 0);
  color: black;
  border: none;
  cursor: pointer;
  border-radius: 14px;
  padding: 30px;
  font-family: "Indie Flower";
  font-size: 30px;
  font-weight: bold;
  margin-right: 20px;
}

button:hover {
  background-color: rgb(253, 255, 156);
  transform: scale(1.1);
  color: black;
}

.game-area {
  margin-top: 54px;
  font-size: 30px;
  font-weight: bolder;
}
</style>
