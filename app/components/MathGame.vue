<script setup>
import { ref, onMounted } from "vue";

const props = defineProps({
  operation: String,
  difficulty: String,
  subType: Number,
  playerName: String
});

const emit = defineEmits(["close", "resetAll"]);

const resetToStart = () => {
    emit("resetAll");
};

const numberA = ref(0);
const numberB = ref(0);
const score = ref(0);
const errors = ref(0);
const currentQuestionCount = ref(1);
const maxQuestions = 20;
const isGameOver = ref(false);

const userAnswer = ref("");
const feedbackMessage = ref("");
const isCorrect = ref(null);
const answerInput = ref(null);

const mathSymbols = {
  addition: "+",
  subtraction: "-",
  multiplication: "*",
  division: ":",
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

const successMessages = [
  "Výborně!",
  "Jen tak dál",
  "Skvělá práce",
  "Jsi šikulka",
  "Perfektní!"
];
const errorMessages = [
  "Zkus to znovu, to dáš!",
  "Skoro to bylo",
  "Těsně vedle"
];

const setFocus = () => {
  setTimeout(() => {
    if (answerInput.value) answerInput.value.focus();
  }, 50);
};

const handleEnter = () => {
  if (isCorrect.value === true) {
    generate();
  } else {
    checkAnswer();
  }
};

const generate = () => {
  userAnswer.value = "";
  isCorrect.value = null;
  feedbackMessage.value = "";

  let max = 50;
  if (props.difficulty === "medium") max = 1000;
  if (props.difficulty === "hard") max = 100000;

  if (props.operation === "addition") {
    const totalResult = Math.floor(Math.random() * (max - 2)) + 2;
    numberA.value = Math.floor(Math.random() * (totalResult - 1)) + 1;
    numberB.value = totalResult - numberA.value;
  } else if (props.operation === "subtraction") {
    numberA.value = Math.floor(Math.random() * max) + 1;
    numberB.value = Math.floor(Math.random() * max) + 1;
    if (numberB.value > numberA.value) {
      let temp = numberA.value;
      numberA.value = numberB.value;
      numberB.value = temp;
    }
  } else if (props.operation === "multiplication") {
    let multiLimit = 10;
    if (props.difficulty === "medium") multiLimit = 20;
    if (props.difficulty === "hard") multiLimit = 100;

    if (props.difficulty === "easy" && props.subType) {
      numberB.value = props.subType;
      numberA.value = Math.floor(Math.random() * 10) + 1;
    } else {
      numberA.value = Math.floor(Math.random() * multiLimit) + 1;
      numberB.value = Math.floor(Math.random() * 10) + 1;
    }
  } else if (props.operation === "division") {
    let divisionLimit = 10;
    if (props.difficulty === "medium") divisionLimit = 20;
    if (props.difficulty === "hard") divisionLimit = 50;

    const result = Math.floor(Math.random() * divisionLimit) + 1;
    let b;

    if (props.difficulty === "easy" && props.subType) {
      b = props.subType;
    } else {
      b = Math.floor(Math.random() * 9) + 2;
    }

    numberA.value = result * b;
    numberB.value = b;
  }

  setFocus();
};

const checkAnswer = () => {
  let correctResult;

  if (props.operation === "addition") {
    correctResult = numberA.value + numberB.value;
  } else if (props.operation === "subtraction") {
    correctResult = numberA.value - numberB.value;
  } else if (props.operation === "multiplication") {
    correctResult = numberA.value * numberB.value;
  } else if (props.operation === "division") {
    correctResult = numberA.value / numberB.value;
  }

  if (Number(userAnswer.value) === correctResult) {
    isCorrect.value = true;
    score.value++;
    feedbackMessage.value =
      successMessages[Math.floor(Math.random() * successMessages.length)];

    setTimeout(() => {
      if (currentQuestionCount.value < maxQuestions) {
        currentQuestionCount.value++;
        generate();
      } else {
        isGameOver.value = true;
      }
    }, 1200);
  } else {
    isCorrect.value = false;
    errors.value++;
    feedbackMessage.value =
      errorMessages[Math.floor(Math.random() * errorMessages.length)];
  }
};

const goHome = () => emit("close");

onMounted(() => {
  generate();
});
</script>

<template>
  <div class="game-screen">
    <div class="game-screen-header">
        <button class="back-button" @click="goHome">Menu</button>
        <div class="progress-container">
            <div v-for="n in maxQuestions" :key="n" class="star-step" :class="{'filled': n < currentQuestionCount, 'active' : n ===currentQuestionCount}">⭐</div>
        </div>
        <div class="player-info">
            Hraje: <span>{{ props.playerName }}</span>
        </div> 
    </div>
    <div v-if="!isGameOver">
      <div class="math-problem">
        {{ numberA }} {{ mathSymbols[props.operation] }} {{ numberB }} =
        <input
          ref="answerInput"
          v-model="userAnswer"
          type="number"
          :class="{ correct: isCorrect === true, wrong: isCorrect === false }"
          placeholder=""
          class="answer-input"
          @keyup.enter="handleEnter"
        />
      </div>

      <div v-if="feedbackMessage" class="feedback-container">
        <p :class="['feedback-text',{ success: isCorrect === true, error: isCorrect === false }, ]"> {{ feedbackMessage }}</p>
      </div>
      <div class="actions">
        <button v-if="isCorrect !== true" @click="checkAnswer">
          Zkontrolovat
        </button>
        <button v-if="isCorrect === true" @click="generate">
          Další příklad
        </button>
      </div>
    </div>
    <div v-else class="game-over-screen">
      <h1>🎉 HOTOVO!</h1>
      <p><strong>Skvělá práce. Jen tak dál.</strong></p>

      <div class="stats">
        <p class="stats-player"><strong>Hráč: </strong>{{ props.playerName }}</p>
        <p class="stats-info"><strong>Procvičili jsme: </strong>{{ operationNames[props.operation] }}</p>
        <p class="stats-info"><strong>Obtížnost: </strong>{{ difficultyNames[props.difficulty] }}</p>
        <p class="stats-score"><strong>Správně: </strong>{{ score }} ✅</p>
        <p class="stats-errors"><strong>Chybně: </strong>{{ errors }} ❌</p>

        <button class="reset-button" @click="resetToStart">Změna hráče</button>
      </div>
    </div>
    <video v-if="isGameOver" autoplay muted loop class="background-video">
        <source src="/pics/confetti.mp4" type="video/mp4">
    </video>
  </div>
</template>

<style scoped>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type="number"] {
  -moz-appearance: textfield;
}

.game-screen {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(180deg, rgb(0, 0, 71) 0%, rgb(100, 100, 255) 100%);
  min-height: 100vh;
  padding-top: 80px;
}

.game-screen-header {
    display: flex;
    box-sizing: border-box;
    justify-content: space-between;
    position: absolute;
    top: 0;
    left: 0;
    align-items: center;
    width: 100%;
    padding: 10px 20px;
}

.back-button {
  margin: 0;
  top: 20px;
  left: 20px;
  font-size: 1.5rem;
  z-index: 10;
  background-color:rgb(255, 242, 0);
}

.progress-container {
    display: flex;
    flex: 2;
    justify-content: center;
    gap: 5px;
}

.star-step {
  font-size: 1.2rem;
  filter: grayscale(1) opacity(0.3);
}
.star-step.filled { filter: grayscale(0) opacity(1); }
.star-step.active { filter: grayscale(0) opacity(1); transform: scale(1.3); }

.star-step.filled {
  filter: grayscale(0) opacity(1);
  animation: starCelebrate 0.5s ease-out;
}

@keyframes starCelebrate {
  0% { transform: scale(1); filter: brightness(1); }
  50% { 
    transform: scale(1.8) rotate(120deg);
    filter: brightness(2) drop-shadow(0 0 15px white);}
  100% { transform: scale(1); filter: brightness(1); }
}

@keyframes pulse {
  from { transform: scale(1.1); filter: drop-shadow(0 0 2px gold); }
  to { transform: scale(1.4); filter: drop-shadow(0 0 10px gold); }
}

.star-step.active {
  filter: grayscale(0) opacity(1);
  animation: pulse 0.8s infinite alternate;
}

.player-info {
  font-family: "Indie Flower";
  margin: 0;
  padding: 5px 15px;
  font-size: 2rem;
  padding: 20px;
  background: none;
  color: rgb(255, 242, 0);
  font-weight: bold;
}

.actions {
    display: flex;
    justify-content: center;
    align-items: center;
}

.math-problem {
  font-size: 60px;
  color: gold;
}

.answer-input {
  width: 120px;
  font-size: 40px;
  background-color: transparent;
  color: gold;
  font-size: 50px;
  border: none;
  text-align: center;
  outline: none;
  padding: 5px;
}

.feedback-container {
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: "Indie Flower";
  margin-top: 20px;
}

.feedback-text {
  font-size: 3rem;
}

.feedback-text.success {
  color: #2ecc71;
}

.feedback-text.error {
  color: #ff7675;
}

button {
  background-color: rgb(255, 242, 0);
  border: none;
  cursor: pointer;
  border-radius: 14px;
  padding: 10px;
  margin-top: 50px;
  font-family: "Indie Flower";
  font-size: 30px;
  font-weight: bold;
}

button:hover {
  background-color: rgb(253, 255, 156);
  transform: scale(1.1);
  color: black;
}

.game-over-screen {
    position: relative;
    font-family: "Indie Flower";
    font-size: 2rem;
    border-radius: 20px;
    padding: 40px;
    bottom: 50px;
    text-align: center;
    box-shadow: 10px 10px 20px rgba(0, 0, 0, 0.5);
    filter: drop-shadow(0 0 10px rgba(255, 215, 0, 0.5));
    animation: fadeInScale 0.9s ease-out;
    z-index: 10;
    background-color: rgb(250, 239, 212);
}

.game-over-screen h1 {
    margin: 0;
}

.background-video {
    position: absolute;
    height: 100%;
    width: 100%;
    top: 0;
    left: 0;
    object-fit: cover;
    z-index: 0;
}

.stats {
    padding: 50px;
    background-color: rgb(255, 253, 235);
    border-radius: 20px;
}

.stats-score {
    color: #2ecc71;
}
.stats-errors {
    color: #ff7675;
}

@keyframes fadeInScale {
  0% {
    opacity: 0; 
    transform: scale(0.9); 
  }
  70% {
    opacity: 1; 
    transform: scale(1.1); 
  }
  100% {
    opacity: 1; 
    transform: scale(1); 
  }
}
</style>
