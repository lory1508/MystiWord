<template>
  <div class="flex flex-col items-center justify-center pt-6 wordle-container">
    <div><img src="/logo.png" class="w-40" /></div>
    <div class="grid w-fit">
      <div 
        v-for="(row, rowIndex) in guesses" 
        :key="rowIndex" 
        class="row w-fit"
      >
        <div 
          v-for="(letter, colIndex) in row" 
          class="rounded-lg"
          :key="colIndex" 
          :class="['cell', getLetterClass(rowIndex, colIndex)]"
        >
          {{ letter }}
        </div>
      </div>
    </div>
    <div class="flex flex-row justify-center w-full gap-4">
      <input 
        v-model="currentGuess" 
        maxlength="5" 
        placeholder="Type your guess" 
        class="p-2 border rounded-lg border-zinc-400"
        @keyup.enter="submitGuess" 
        :disabled="gameOver" 
      />
      <button 
        @click="submitGuess" 
        class="px-6 py-2 font-semibold text-white rounded-lg bg-emerald-600 w-fit" 
        :disabled="gameOver"
      >
        Submit
      </button>
    </div>
    <p 
      v-if="gameMessage" 
      class="game-message"
    >
      {{ gameMessage }}
    </p>
  </div>
</template>

<script setup>
const wordToGuess = ref("peace");
const guesses = ref([...Array(6)].map(() => Array(5).fill("")));
const currentGuess = ref("");
const guessIndex = ref(0);
const gameMessage = ref("");
const gameOver = ref(false);

const submitGuess = () => {
  if (currentGuess.value.length !== 5 || gameOver.value) return;
  
  const guessArray = currentGuess.value.toUpperCase().split("");
  guesses.value[guessIndex.value] = [...guessArray];
  
  if (currentGuess.value.toUpperCase() === wordToGuess.value) {
    gameMessage.value = "🎉 Congratulations! You guessed the word!";
    gameOver.value = true;
  } else if (guessIndex.value === 5) {
    gameMessage.value = `😞 Game over! The word was ${wordToGuess.value}.`;
    gameOver.value = true;
  }
  
  guessIndex.value++;
  currentGuess.value = "";
};

const getLetterClass = (row, col) => {
  const letter = guesses.value[row][col];
  if (!letter) return '';
  if (letter === wordToGuess.value[col]) return 'correct';
  if (wordToGuess.value.includes(letter)) return 'present';
  return 'absent';
};
</script>

<style>
.wordle-container {
  /* max-width: 400px; */
  margin: auto;
  text-align: center;
  font-family: Arial, sans-serif;
}
.grid {
  display: grid;
  grid-template-rows: repeat(6, 1fr);
  gap: 5px;
  margin: 20px 0;
}
.row {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 5px;
}
.cell {
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5em;
  font-weight: bold;
  border: 2px solid #ccc;
  text-transform: uppercase;
  transition: background-color 0.3s ease-in-out;
}
.correct { background-color: green; color: white; animation: pop 0.3s ease-in-out; }
.present { background-color: orange; color: white; animation: pop 0.3s ease-in-out; }
.absent { background-color: gray; color: white; animation: fade 0.3s ease-in-out; }
.submit-btn {
  margin-top: 10px;
  padding: 10px;
  font-size: 1em;
  cursor: pointer;
  transition: background-color 0.3s ease-in-out;
}
.submit-btn:disabled {
  background-color: #aaa;
  cursor: not-allowed;
}
.game-message {
  font-size: 1.2em;
  font-weight: bold;
  margin-top: 10px;
}
@keyframes pop {
  0% { transform: scale(0.8); }
  100% { transform: scale(1); }
}
@keyframes fade {
  0% { opacity: 0; }
  100% { opacity: 1; }
}
</style>
