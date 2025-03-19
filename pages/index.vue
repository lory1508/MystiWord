<template>
  <div class="flex flex-col items-center justify-center max-w-md gap-4 p-4 mx-auto font-sans text-center w-fit">
    <img src="/logo.png" class="w-40" />
    <div class="flex flex-col items-center justify-center gap-2">
      <div 
        v-for="(row, rowIndex) in guesses" 
        :key="rowIndex" 
        class="flex flex-row gap-2 w-fit"
      >
        <div 
          v-for="(letter, colIndex) in row" 
          :key="colIndex" 
          :class="['w-12 h-12 rounded-lg flex items-center justify-center text-xl font-bold border-2', getLetterClass(rowIndex, colIndex)]"
        >
          {{ letter }}
        </div>
      </div>
    </div>
    <div class="flex flex-col items-center justify-center w-full gap-2">
      <div class="relative w-full">
        <input 
          v-model="currentGuess" 
          maxlength="5" 
          placeholder="Type your guess" 
          @keyup.enter="submitGuess" 
          :disabled="gameOver" 
          class="w-full p-2 text-lg text-center border rounded-md disabled:bg-gray-300" 
        />
        <span class="absolute text-sm text-gray-500 transform -translate-y-1/2 right-2 top-1/2">
          {{ currentGuess.length }}/5
        </span>
      </div>
      <button 
        @click="submitGuess" 
        :disabled="gameOver" 
        class="w-full py-2 font-bold text-white bg-blue-600 rounded-md hover:bg-blue-700 disabled:bg-gray-400"
      >
        Submit
      </button>
      <small class="text-zinc-400">Developed by <a href="https://github.com/lory1508" target="_blank" class="underline underline-offset-2">Lorenzo Galassi</a></small>
    </div>
    <p v-if="gameMessage" class="mt-4 text-lg font-semibold">{{ gameMessage }}</p>
  </div>
</template>

<script setup>
const wordToGuess = ref("PEACE");
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
  if (!letter) return 'border-gray-300';
  if (letter === wordToGuess.value[col]) return 'bg-green-500 text-white border-green-500';
  if (wordToGuess.value.includes(letter)) return 'bg-yellow-500 text-white border-yellow-500';
  return 'bg-gray-500 text-white border-gray-500';
};
</script>
