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
  </div>
  <div v-if="gameMessage" class="flex flex-col items-center justify-center max-w-md gap-4 p-4 mx-auto font-sans text-center w-fit">
    <p class="mt-4 text-lg font-semibold">
      <div>
        {{ gameMessage }}
      </div>
    </p>
    <div class="flex flex-col justify-start gap-2 p-2 mt-2 font-sans rounded-md text-start bg-zinc-100">
      <ol>
        <li v-for="(definition, index) in definitions" :key="`definition_${index}`" class="flex flex-col gap-2 mb-8">
          <span class="font-mono">
            - {{ definition.definition }}</span>
          <span class="text-gray-500">{{ definition.example }}</span>
          <div v-if="definition.synonyms.length">
            <span class="font-bold">Synonyms:</span>
            <span>{{ definition.synonyms.join(", ") }}</span>
          </div>
        </li>
      </ol>
    </div>
  </div>
</template>

<script setup>
const WORD_LENGTH = 5;
const API_DEFINITIONS = "https://api.dictionaryapi.dev/api/v2/entries/en/";
const API_WORD_GENERATOR = "https://random-word-api.vercel.app/api";

const wordToGuess = ref("");
const guesses = ref([...Array(6)].map(() => Array(5).fill("")));
const currentGuess = ref("");
const guessIndex = ref(0);
const gameMessage = ref("");
const gameOver = ref(false);
const definitions = ref([]);
const invalidGuess = ref(false);

watch(() => currentGuess.value, (value) => {
  if(invalidGuess.value) {
    invalidGuess.value = false;
    gameMessage.value = "";
  }
});

watch(() => gameOver.value, async() => {
  try{
    if (gameOver.value) {
      const response = await fetch(`${API_DEFINITIONS}${wordToGuess.value}`);
      const data = await response.json();
      definitions.value = data[0].meanings[0].definitions.slice(0, 3);
    }
  } catch (error) {
    console.error(error);
  }
});

const submitGuess = async() => {
  if (currentGuess.value.length !== 5 || gameOver.value) return;

  try{
    const response = await fetch(`${API_DEFINITIONS}${currentGuess.value}`);
    const data = await response.json();
    if(!data.length) {
      invalidGuess.value = true;
      gameMessage.value = "🤔 The word is not in the dictionary. Try again!";
      return;
    }
    
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
  } catch (error) {
    console.error(error);
  }
};

const getLetterClass = (row, col) => {
  const letter = guesses.value[row][col];
  if (!letter) return 'border-gray-300';
  if (letter === wordToGuess.value[col]) return 'bg-green-500 text-white border-green-500';
  if (wordToGuess.value.includes(letter)) return 'bg-yellow-500 text-white border-yellow-500';
  return 'bg-gray-500 text-white border-gray-500';
};

onMounted(async() => {
  try{
    const response = await fetch(`${API_WORD_GENERATOR}?words=1&type=uppercase&length=${WORD_LENGTH}`);
    const data = await response.json();
    wordToGuess.value = data[0];
  } catch (error) {
    console.error(error);
  }
})
</script>
