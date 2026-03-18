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

const submitName = () => {
  if (playerName.value.trim().length >= 2) {
    playerNameEntered.value = true;
  }
};

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
        @keyup.enter="submitName"
        class="name-input"
      />
      <button v-if="playerName.trim().length >= 2" @click="playerNameEntered = true">
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
            Máš vybráno {{ operationNames[chosenMathOperation] }} <span v-if="chosenSubtype">{{ chosenSubtype }}</span>, obtížnost {{ difficultyNames[chosenDifficulty] }}
          </h2>
          <button class="game-start-btn" @click="console.log('click to start');showStartMessage = false; gameStarted = true;"> Jdeme na to! 🧠 </button>
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

@media (min-width: 344px) {
  .app-container {
    background-size: cover;
    background-repeat: no-repeat;
    background-attachment: fixed;
    background-position: center;
    min-height: 100vh;
    width: 100%;
  }

  .header h1 {
    text-align: center;
    font-size: 1.6rem;
    margin-top: 40px;
  }

  .math-operation h2,
  .difficulty-selection h2,
  .welcome-screen h2 {
    font-size: 1.4rem;
    text-align: center;
    margin-bottom: 20px;
  }

  .name-selection-screen button {
    padding: 10px;
    font-size: 1.2rem;
    font-weight: bold;
    margin: 20px;
  }

  .math-operation button {
    width: calc(50% - 20px);
    margin: 10px;
    padding: 8px;
    font-size: 1.2rem;
    max-width: 120px;
  }

  .controls {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .controls button {
    margin: 10px;
    padding: 8px;
    font-size: 1.2rem;
    max-width: 120px;
  }

  .math-operation .back-step-button {
    position: absolute;
    top: 0;
    left: 0;
    font-size: 0.8rem;
    padding: 4px;
    z-index: 100;
    max-width: 90px;
  }

  .difficulty-selection .back-step-button {
    position: absolute;
    top: 0;
    left: 0;
    padding: 2px;
    font-size: 1rem;
  }

  .dropdown-tongue {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    padding: 8px;
    gap: 8px;
    max-width: 140px;
  }

  .dropdown-tongue button {
    padding: 10px;
    min-width: 40px;
    font-size: 1rem;
    margin: 0;
  }

  .dropdown-tongue p {
    font-size: 1.4rem;
  }

  .welcome-screen .back-step-button {
    position: absolute;
    top: 0;
    left: 0;
    padding: 2px;
    font-size: 1rem;
  }

  .name-selection-screen {
    display: flex;
    padding-top: 40px;
    align-items: center;
    flex-direction: column;
  }

  .name-selection-screen h2 {
    font-size: 1.8rem;
    text-align: center;
  }

  .name-input {
    font-size: 1.2rem;
    padding: 10px;
    height: 30px;
    text-align: center;
  }
}

@media (min-width: 768px) {
  .app-container {
    background-size: cover;
    background-repeat: no-repeat;
    background-attachment: fixed;
    background-position: center;
    min-height: 100vh;
    width: 100%;
  }

  .name-input {
    font-size: 1.8rem;
  }

  .name-selection-screen h2 {
    font-size: 2.6rem;
    text-align: center;
  }

  .name-selection-screen button {
    padding: 20px;
    font-size: 1.8rem;
    margin: 40px;
  }

  .math-operation .back-step-button {
    padding: 4px;
    font-size: 1rem;
  }

  .header h1 {
    display: block;
    text-align: center;
    max-width: 500px;
    margin: 0 auto;
    font-size: 2.6rem;
    padding-top: 10px;
  }

  .controls {
    margin-top: 20px;
  }

  .controls h2 {
    font-size: 2rem;
  }

  .math-operation {
    flex-direction: row;
    justify-content: center;
    flex-wrap: nowrap;   
    gap: 10px;
    width: 100%;
  }

  .math-operation button {
    box-sizing: border-box;
    padding: 20px 5px;
    font-size: 1.4rem;
    width: 160px;
  }

  .difficulty-selection {
    flex-direction: row;
    justify-content: center;
    flex-wrap: nowrap;   
    gap: 10px;
    width: 100%;
  }

  .difficulty-selection .back-step-button {
    position: absolute;
    top: 0;
    left: 0;
    padding: 6px;
    font-size: 1.2rem;
  }

  .difficulty-wrapper button {
    box-sizing: border-box;
    padding: 20px 5px;
    font-size: 1.5rem;
    width: 160px;
  }

  .dropdown-tongue {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    padding: 4px;
    gap: 8px;
    max-width: 260px;
  }

  .dropdown-tongue button {
    padding: 12px;
    width: 100px;
    font-size: 1.4rem;
  }

  .dropdown-tongue p {
    font-size: 1.6rem;
  }

  .welcome-screen .back-step-button {
    position: absolute;
    top: 0;
    left: 0;
    padding: 6px;
    font-size: 1.2rem;
  }

  .welcome-screen .game-start-btn {
    padding: 5px;
    font-size: 1.4rem;
    width: 400px;
  }
}

@media (min-width: 1020px) {
  .name-selection-screen h2 {
    font-size: 3rem;
  }

  .name-selection-screen {
    display: flex;
    padding-top: 20px;
  }

  .name-input {
    font-size: 1.8rem;
    padding: 16px;
    height: 40px;
    margin-top: 40px;
  }

  .name-selection-screen button {
    margin: 80px;
  }

  .math-operation .back-step-button {
    font-size: 1.2rem;
    max-width: 140px;
    padding: 6px;
  }

  .header h1 {
    text-align: center;
    font-size: 3rem;
    max-width: 100%;
  }

  .math-operation h2 {
    font-size: 2.2rem;
  }

  .math-operation button {
    margin: 10px;
    padding: 30px 10px;
    font-size: 1.4rem;
  }

  .difficulty-selection h2 {
    font-size: 2.2rem;
  }

  .difficulty-selection button {
    margin: 10px;
    padding: 30px 10px;
    font-size: 1.4rem;
    white-space: nowrap;
    margin-bottom: 0;
  }

  .dropdown-tongue {
    gap: 10px;
    padding: 10px;
    max-width: 180px;
    margin-top: 0;
  }

  .dropdown-tongue button {
    padding: 10px 8px;
    font-size: 1.4rem;
    margin: 0;
    width: 50px;
    height: 50px;
  }

  .welcome-screen .game-start-btn {
    padding: 20px;
    font-size: 1.5rem;
    max-width: 220px;
  }
}
</style>
