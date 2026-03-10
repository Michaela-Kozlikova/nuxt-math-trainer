<script setup>
import { ref, onMounted } from "vue";

const props = defineProps(["operation", "difficulty"]);

const numberA = ref(0);
const numberB = ref(0);
const score = ref(0);

const mathSymbols = {
    addition: '+',
    subtraction: '-',
    multiplication: '*',
    division: ':'
};

const answerInput = ref(null);

const setFocus = () => {
    setTimeout(() => { 
        if (answerInput.value) {
            answerInput.value.focus();
        }
    }, 50);
};

const handleEnter = () => {
    if(isCorrect.value === true) {
        generate();
    } else {
        checkAnswer();
    }
};

const emit = defineEmits(['close']);
const goHome = () => {
    emit("close");
}

 const userAnswer = ref ("");
    const successMessages = ["Výborně!", "Jen tak dál", "Skvělá práce", "Jsi šikulka", "Perfektní!", "Jsi hvězda"];
    const errorMessages = ["Zkus to znovu, to dáš!", "Skoro to bylo", "Těsně vedle", "To není správně, zkusme to znovu"];
    const feedbackMessage = ref("");
    const isCorrect = ref(null);

const generate = () => {
    userAnswer.value = "";
    isCorrect.value = null;
    feedbackMessage.value = "";

    let max = 50;
    if (props.difficulty === 'medium') max = 1000;
    if (props.difficulty === 'hard') max = 100000;

    if (props.operation === 'addition') {
        const totalResult = Math.floor(Math.random() * (max - 2)) + 2;
        numberA.value = Math.floor(Math.random() * (totalResult -1)) + 1;
        numberB.value = totalResult - numberA.value;
        }
    else if (props.operation === 'subtraction') {
        numberA.value = Math.floor(Math.random() * max) + 1;
        numberB.value = Math.floor(Math.random() * max) + 1;
        if (numberB.value > numberA.value) {
            let temp = numberA.value;
            numberA.value = numberB.value;
            numberB.value = temp;
            }
        }
    else if (props.operation === 'multiplication') {
        let multiLimit = 10;
        if (props.difficulty === 'medium') multiLimit = 20;
        if (props.difficulty === 'hard') multiLimit = 100;

        numberA.value = Math.floor(Math.random() * multiLimit) + 1;
        numberB.value = Math.floor(Math.random() * 10) + 1;
        }
    else if (props.operation === 'division') {
        let divisionLimit = 10;
        if (props.difficulty === 'medium') divisionLimit = 20;
        if (props.difficulty === 'hard') divisionLimit = 50;

        const result = Math.floor(Math.random() * divisionLimit) + 1;
        const b = Math.floor(Math.random() * 10) + 1;

        numberA.value = result * b;
        numberB.value = b;
    }
    
        setFocus();
    };

const checkAnswer = () => {
    let correctResult;

    if (props.operation === 'addition') {
        correctResult = numberA.value + numberB.value;
    } else if (props.operation === 'subtraction') {
        correctResult = numberA.value - numberB.value;
    } else if (props.operation === 'multiplication') {
        correctResult = numberA.value * numberB.value;
    } else if (props.operation === 'division') {
        correctResult = numberA.value / numberB.value;
    }

    if (Number(userAnswer.value) === correctResult) {
        isCorrect.value = true;
        score.value++;
        feedbackMessage.value = successMessages[Math.floor(Math.random() * successMessages.length)];
    } else {
        isCorrect.value = false;
        feedbackMessage.value = errorMessages[Math.floor(Math.random() * errorMessages.length)];
    }
};

onMounted(() => {
  generate();
});
</script>

<template>
  <div class="game-screen">
    <button class="back-button" @click="goHome"> <<- </button>

    <div class="score-board">
       <span class="score-stars">⭐ ⭐ ⭐</span>
    Body: <span>{{ score }}</span> 
    </div>

    <div class="math-problem">{{ numberA }}  {{mathSymbols[props.operation]}} {{ numberB }} = 
        <input 
        ref="answerInput"
        v-model="userAnswer"
        type="number"
        :class="{ 'correct' : isCorrect === true, 'wrong' : isCorrect === false}"
        placeholder=""
        class="answer-input"
        @keyup.enter="handleEnter"/>
    </div>

    <div v-if="feedbackMessage" class="feedback-container">
        <p :class="['feedback-text', {'success': isCorrect === true, 'error': isCorrect === false}]">{{  feedbackMessage }}</p>
    </div>
    <div class="actions">
        <button v-if="isCorrect !== true" @click="checkAnswer">Zkontrolovat</button>
        <button v-if="isCorrect === true" @click="generate">Další příklad</button>
    </div>
  </div>
</template>

<style scoped>

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type=number] {
  -moz-appearance: textfield;
}
.game-screen {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: rgb(5, 5, 139);
    min-height: 100vh;
}

.back-button {
    position: absolute;
    margin-top: 0;
    top: 20px;
    left: 20px;
    font-size: 2rem;
}

.score-stars {
    display: flex;
}

.score-board {
    font-family: "Indie Flower";
    position: absolute;
    top: 10px;
    right: 20px;
    font-size: 2rem;
    background-color: rgb(255, 242, 0);
    padding: 20px;
    border-radius: 20px;
    text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
}

.math-problem {
    font-size: 60px;
    color: gold;
}

.answer-input {
    width: 120px;
    font-size: 40px;
    background-color: rgb(82, 113, 175);
    color: gold;
    font-size: 50px;
    border: none;
    text-align: center;
    outline: none;
    padding: 5px;
}

.feedback-container {
    font-family: "Indie Flower";
    margin-top: 40px;
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
  color: black;
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

</style>
