<script setup>
import { ref } from "vue";
import MathGame from "~/components/MathGame.vue";

const playerName = ref("");
const playerNameEntered = ref(false);
const chosenMathOperation = ref(null);
const chosenDifficulty = ref(null);
const chosenSubtype = ref(null);
const showStartMessage = ref(false);
const gameStarted = ref(false);
const isDropDownOpen = ref(false);

const operationNames = {
  addition: "sčítání",
  subtraction: "odčítání",
  multiplication: "násobení",
  division: "dělení",
};

const difficultyNames = {
  easy: "lehká",
  medium: "střední",
  hard: "těžká",
};

const selectSubtype = (num) => {
  chosenDifficulty.value = "easy";
  chosenSubtype.value = num;
  showStartMessage.value = true;
  isDropDownOpen.value = false;
};

const handleEasyClick = () => {
  if (
    chosenMathOperation.value === "multiplication" ||
    chosenMathOperation.value === "division"
  ) {
    isDropDownOpen.value = !isDropDownOpen.value;
  } else {
    chosenDifficulty.value = "easy";
    showStartMessage.value = true;
  }
};

const resetToHome = () => {
  gameStarted.value = false;
  chosenMathOperation.value = null;
  chosenDifficulty.value = null;
  chosenSubtype.value = null;
  showStartMessage.value = false;
  isDropDownOpen.value = false;
};

const handleFullReset = () => {
  gameStarted.value = false;
  showStartMessage.value = false;
  chosenDifficulty.value = null;
  chosenMathOperation.value = null;
  playerName.value = "";
  playerNameEntered.value = false;
}
</script>

<template>
  <div v-if="!gameStarted" class="app-container">
    <link
      href="https://fonts.googleapis.com/css2?family=Hanalei+Fill&family=Indie+Flower&family=Mansalva&display=swap"
      rel="stylesheet"/>

    <div v-if="!playerNameEntered" class="name-selection-screen">
      <h2>Ahoj! Jak se jmenuješ?</h2>
      <input
        v-model="playerName"
        placeholder="Napiš své jméno"
        @keyup.enter="playerNameEntered = true"
        class="name-input"
      />
      <button v-if="playerName.length > 1" @click="playerNameEntered = true">
        Pokračovat
      </button>
    </div>

    <div v-else class="main-menu">
      <div class="header">
        <h1>Pojďme si společně procvičit počítání</h1>
      </div>
      <div class="controls">
        <div v-if="!chosenMathOperation" class="math-operation">
          <button class="back-step-button" @click="playerNameEntered = '' ">Změna jména</button>
          <h2>Co si dnes zkusíme?</h2>
          <button :style="{ animationDelay: '0.1s' }" @click="chosenMathOperation = 'addition'">Sčítání</button>
          <button :style="{ animationDelay: '0.3s' }" @click="chosenMathOperation = 'subtraction'">Odčítání</button>
          <button :style="{ animationDelay: '0.4s' }" @click="chosenMathOperation = 'multiplication'">Násobení</button>
          <button :style="{ animationDelay: '0.5s' }" @click="chosenMathOperation = 'division'">Dělení</button>
        </div>
        <div v-else-if="!chosenDifficulty" class="difficulty-selection">
          <button class="back-step-button" @click="chosenMathOperation = null">⬅</button>
          <h2>Vyber obtížnost</h2>
          <div class="difficulty-wrapper">
            <div class="dropdown-container">
              <button
                :style="{ animationDelay: '0.1s' }"
                @click="handleEasyClick"
                :class="{ 'btn-active': isDropDownOpen }">Lehká 🌱</button>

              <div v-if="isDropDownOpen" class="dropdown-tongue">
                <p>
                  {{ chosenMathOperation === "multiplication" ? "vyber násobky" : "vyber dělitele" }}
                </p>
                <button v-for="n in 8" :key="n" @click="selectSubtype(n + 1)"> {{ n + 1 }} </button>
              </div>
            </div>
            <button
              :style="{ animationDelay: '0.3s' }"
              @click="chosenDifficulty = 'medium'; chosenSubtype = null; showStartMessage = true;">Střední 💡</button>
            <button :style="{ animationDelay: '0.4s' }" @click="chosenDifficulty = 'hard'; chosenSubtype = null; showStartMessage = true;">Těžká 🚀</button>
          </div>
        </div>
        <div v-else-if="showStartMessage" class="welcome-screen">
          <button class="back-step-button" @click="chosenDifficulty = null; showStartMessage = false">⬅</button>
          <h2>
            Máš vybráno {{ operationNames[chosenMathOperation] }} <span v-if="chosenSubtype">{{ chosenSubtype }}</span> a obtížnost {{ difficultyNames[chosenDifficulty] }}
          </h2>
          <button @click="console.log('klik na start');showStartMessage = false; gameStarted = true;"> Jdeme na to! 🧠 </button>
        </div>
      </div>
    </div>
  </div>
  <MathGame
    v-else
    :playerName="playerName"
    :operation="chosenMathOperation"
    :difficulty="chosenDifficulty"
    :subType="chosenSubtype"
    @close="resetToHome"
    @resetAll="handleFullReset"
  />
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

.name-selection-screen {
  display: flex;
  padding-top: 40px;
  align-items: center;
  flex-direction: column;
  background: linear-gradient(20deg, rgb(198, 198, 255) 0%, rgb(30, 30, 180) 100%);
  height: 100vh;
  width: 100vw;
}

.name-selection-screen h2 {
  color: rgb(255, 242, 0);
  font-size: 3rem;
  text-shadow: 2px 2px 10px rgba(0, 0, 0, 0.5);
}

.name-input {
  font-family: 'Indie Flower';
  font-size: 1.5rem;
  padding: 16px;
  height: 40px;
  text-align: center;
  border: none;
  border-radius: 20px;
  background-color: rgb(180, 184, 248);
}

.name-input:hover {
  transform: scale(1.06);
}

.name-selection-screen button {
  margin: 80px;
}

.back-step-button {
  position: absolute;
  background-color: rgba(255, 255, 255, 0.2);
  color: white;
  font-size: 1rem;
  padding: 10px 20px;
  margin: 0;
  top: 20px;
  left: 20px;
  border: 2px solid white;
}

.back-step-button:hover {
  background-color: white;
  color: black;
  transform: scale(1.02);
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

.dropdown-container {
  display: inline-block;
  position: relative;
  vertical-align: top;
}

.dropdown-tongue {
  background-color: transparent;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  padding: 30px;
  border-radius: 14px;
  max-width: 180px;
  margin-left: auto;
  margin-right: auto;
  animation: lickOut 0.5s ease-out forwards;
  transform-origin: top center;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
}

.dropdown-tongue p {
  color: black;
  font-size: 1.5rem;
}

.dropdown-tongue button {
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
  padding: 10px 15px;
  font-size: 20px;
  margin: 0;
  min-width: 50px;
}

.dropdown-tongue button:hover {
  background-color: rgb(253, 255, 156);
  color: black;
  transform: scale(1.1);
}

@keyframes lickOut {
  0% {
    transform: scaleY(0);
    opacity: 0;
  }
  70% {
    transform: scaleY(1.1);
  }
  100% {
    transform: scaleY(1);
    opacity: 1;
  }
}

.btn-active {
  z-index: 10;
  position: relative;
}

.game-area {
  margin-top: 54px;
  font-size: 30px;
  font-weight: bolder;
}
</style>
