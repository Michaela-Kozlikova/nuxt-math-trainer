<script setup>
import { ref, onMounted, computed } from "vue";

const props = defineProps({
  operation: String,
  difficulty: String,
  subType: Number,
  playerName: String,
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
  "Perfektní!",
];
const errorMessages = [
  "Zkus to znovu, to dáš!",
  "Skoro to bylo",
  "Těsně vedle",
];

const emptyMessages = [
  "Haló! Tady je prázdno! 🫙",
  "Napiš aspoň něco, prázdno není výsledek! ✍️",
  "Bez výsledku se dál nepohneme... 🛑",
];

const finalMessage = computed(() => {
  if (score.value >= 18) {
    return "Jsi matematický bůh! 🏆";
  } else if (score.value >= 15) {
    return "Skoro bez chybičky. Skvělá práce. ⭐";
  } else if (score.value >= 11) {
    return " Dobrá práce, ale ještě to trochu potrénujeme? 💪";
  } else {
    return "Chce to ještě trénovat. Pojď si to zkusit znova. 🚀";
  }
});

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

  if (
    userAnswer.value === null ||
    userAnswer.value.toString().trim().length === 0
  ) {
    feedbackMessage.value =
      emptyMessages[Math.floor(Math.random() * emptyMessages.length)];
    return;
  }

  if (Number(userAnswer.value) === correctResult) {
    if (isCorrect.value === null) {
      score.value++;
    }

    isCorrect.value = true;
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
    if (isCorrect.value === null) {
      errors.value++;
    }
    isCorrect.value = false;
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
  <link
    href="https://fonts.googleapis.com/css2?family=Chewy&display=swap"
    rel="stylesheet"
  />
  <div class="game-screen">
    <div class="game-screen-header">
      <button v-if="!isGameOver" class="back-button menu-btn" @click="goHome">
        Menu
      </button>
      <button v-else class="back-button" @click="resetToStart">
        Změna hráče
      </button>
      <div class="progress-container">
        <div
          v-for="n in maxQuestions"
          :key="n"
          class="star-step"
          :class="{
            filled: n < currentQuestionCount,
            active: n === currentQuestionCount,
          }"
        >
          ⭐
        </div>
      </div>
      <div class="player-info">
        Hraje: <span>{{ props.playerName }}</span>
      </div>
    </div>
    <div v-if="!isGameOver" class="game-content">
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
        <p :class="['feedback-text', { success: isCorrect === true, error: isCorrect === false || (isCorrect === null && feedbackMessage !== '') }, ]" >{{ feedbackMessage }}</p>
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
      <p>
        <strong>{{ finalMessage }}</strong>
      </p>

      <div class="stats">
        <p class="stats-player">
          <strong>Hráč: </strong>{{ props.playerName }}
        </p>
        <p class="stats-info">
          <strong>Procvičili jsme: </strong
          >{{ operationNames[props.operation] }}
          <span v-if="props.subType"> {{ props.subType }}</span>
        </p>
        <p class="stats-info">
          <strong>Obtížnost: </strong>{{ difficultyNames[props.difficulty] }}
        </p>
        <p class="stats-score"><strong>Správně: </strong>{{ score }} ✅</p>
        <p class="stats-errors"><strong>Chybně: </strong>{{ errors }} ❌</p>

        <button class="back-button" @click="goHome">Menu</button>
      </div>
    </div>
    <video v-if="isGameOver" autoplay muted loop playsinline webkit-playsinline class="background-video">
      <source src="/pics/confetti.mp4" type="video/mp4" />
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
  background: linear-gradient(
    180deg,
    rgb(0, 0, 71) 0%,
    rgb(100, 100, 255) 100%
  );
  min-height: 100vh;
  padding-top: 80px;
}

.game-screen-header {
  display: flex;
  box-sizing: border-box;
  justify-content: space-between;
  top: 0;
  left: 0;
  align-items: center;
  width: 100%;
  padding: 10px 20px;
}

.back-button {
  background-color: transparent;
  color: white;
  font-size: 1.2rem;
  padding: 10px 20px;
  margin: 0;
  top: 20px;
  left: 20px;
  border: 1px solid white;
  z-index: 10;
}

.back-button:hover {
  background-color: rgba(255, 255, 255, 0.2);
  color: white;
  transform: scale(1.02);
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
.star-step.filled {
  filter: grayscale(0) opacity(1);
}
.star-step.active {
  filter: grayscale(0) opacity(1);
  transform: scale(1.3);
}

.star-step.filled {
  filter: grayscale(0) opacity(1);
  animation: starCelebrate 0.5s ease-out;
}

@keyframes starCelebrate {
  0% {
    transform: scale(1);
    filter: brightness(1);
  }
  50% {
    transform: scale(1.8) rotate(120deg);
    filter: brightness(2) drop-shadow(0 0 15px white);
  }
  100% {
    transform: scale(1);
    filter: brightness(1);
  }
}

@keyframes pulse {
  from {
    transform: scale(1.1);
    filter: drop-shadow(0 0 2px gold);
  }
  to {
    transform: scale(1.4);
    filter: drop-shadow(0 0 10px gold);
  }
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
  display: flex;
  justify-content: center;
  font-family: "Chewy";
  font-size: 60px;
  color: gold;
  gap: 20px;
  width: 100%;
}

.answer-input {
  font-family: "Chewy";
  width: 200px;
  font-size: 60px;
  background-color: transparent;
  color: gold;
  border: none;
  outline: none;
  padding: 0;
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
  width: 600px;
  font-family: "Indie Flower";
  font-size: 2rem;
  border-radius: 20px;
  padding: 20px;
  bottom: 50px;
  text-align: center;
  box-shadow: 10px 10px 20px rgba(0, 0, 0, 0.5);
  filter: drop-shadow(0 0 10px rgba(255, 215, 0, 0.5));
  animation: fadeInScale 0.9s ease-out;
  z-index: 5;
  background-color: rgb(250, 239, 212);
}

.game-over-screen h1 {
  margin: 0;
}

.background-video {
  position: fixed;
  height: 100%;
  width: 100%;
  top: 0;
  left: 0;
  object-fit: cover;
  z-index: 0;
  pointer-events: none;
}

.stats {
  padding: 20px;
  background-color: rgb(255, 253, 235);
  border-radius: 20px;
}

.stats-score {
  color: #2ecc71;
}
.stats-errors {
  color: #ff7675;
}

.stats .back-button {
  background-color: rgb(255, 242, 0);
  color: black;
  padding: 10px;
  font-size: 30px;
}

.stats .back-button:hover {
  background-color: rgb(253, 255, 156);
  transform: scale(1.1);
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

  .game-screen-header {
    display: flex;
    box-sizing: border-box;
    position: relative;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    padding: 10px;
    top: 0;
    left: 0;
    width: 100%;
  }

  .game-screen {
    display: block;
    justify-content: flex-start;
    align-items: center;
    flex-direction: column;
    overflow-x: hidden;
    padding: 0;
    width: 100vw;
  }

  .game-screen-header .back-button {
    margin: 0;
    font-size: 1rem;
    padding: 8px;
    order: 1;
  }

  .progress-container {
    display: grid;
    grid-template-columns: repeat(10, 1fr);
    justify-content: center;
    justify-items: center;
    gap: 4px;
    width: 100%;
    flex-basis: 100%;
    margin: 30px auto 0 auto;
    order: 3;
  }

  .progress-container .star-step {
    font-size: 1rem;
  }

  .player-info {
    margin: 0;
    font-size: 1.2rem;
    width: auto;
    order: 2;
  }

  .game-content {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  .math-problem {
    display: flex;
    position: relative;
    font-size: 1.8rem;
    margin: 60px auto;
    white-space: nowrap;
    width: max-content;
  }

  .answer-input {
    display: inline-block;
    font-size: 1.8rem;
    width: 100%;
    max-width: 100px;
    padding: 0;
  }

  .feedback-container {
    text-align: center;
  }

  .feedback-text {
    font-size: 1.6rem;
  }

  .actions button {
    font-size: 1.4rem;
    padding: 8px;
  }

  .game-over-screen {
    font-size: 1.2rem;
    width: 280px;
    margin: 0 auto;
  }

  .stats .back-button {
    font-size: 1.4rem;
    padding: 8px;
  }


@media (min-width: 768px) {
  .game-screen {
    display: block;
    justify-content: flex-start;
    align-items: center;
    flex-direction: column;
    overflow-x: hidden;
    padding: 0;
    width: 100vw;
  }

  .game-screen-header .back-button {
    margin: 0;
    font-size: 1.2rem;
    padding: 8px;
    order: 1;
  }

  .progress-container {
    display: flex;
    justify-content: center;
    justify-items: center;
    width: 100%;
    margin-bottom: 60px;
  }

  .progress-container .star-step {
    font-size: 1.5rem;
    padding: 2px;
  }

  .player-info {
    margin: 0;
    font-size: 1.5rem;
    width: auto;
  }

  .math-problem {
    display: flex;
    font-size: 3.4rem;
    margin: 60px auto;
    width: 100%;
  }

  .answer-input {
    display: inline-block;
    font-size: 3.4rem;
    width: 100%;
    max-width: 200px;
    padding: 0;
  }

  .feedback-container {
    text-align: center;
  }

  .feedback-text {
    font-size: 2.2rem;
    flex-wrap: wrap;
  }

  .actions button {
    font-size: 1.4rem;
    padding: 12px;
  }

  .game-over-screen {
    font-size: 2rem;
    width: 400px;
    margin: 0 auto;
  }

  .stats .back-button {
    font-size: 1.4rem;
    padding: 8px;
  }
}

@media (min-width: 1020px) {
  .game-screen-header {
    display: flex;
    box-sizing: border-box;
    justify-content: space-between;
    align-items: center;
    flex-wrap: nowrap;
    padding: 10px 20px;
    margin-bottom: 90px;
  }

  .progress-container {
    display: flex;
    justify-content: center;
    margin: 0;
    flex-basis: auto;
    width: auto;
    order: 2;
    flex-grow: 1;
  }

  .progress-container .star-step {
    font-size: 1.8rem;
  }

  .player-info {
    font-size: 2rem;
    padding: 20px;
  }

  .math-problem {
    font-size: 4rem;
    gap: 20px;
    margin-top: 80px;
  }

  .answer-input {
    font-size: 4rem; 
  }

  .feedback-text {
    font-size: 3rem;
  }

  .actions button {
    font-size: 1.8rem;
  }

  .game-over-screen {
    width: 500px;
    font-size: 2.1rem;
    margin-top: -80px;
  }

  .stats .back-button {
    padding: 10px;
    font-size: 1.8rem;
  }
}

</style>
