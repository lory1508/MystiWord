<template>
  <Modal
    ref="hintModalRef"
    @close="closeModal('hint')"
    @confirm="showOneLetter"
    :show-confirm="availableHints > 0"
  >
    <div
      class="flex flex-col items-center justify-center max-w-md gap-4 p-4 mx-auto font-sans text-center w-fit"
    >
      <div class="mt-4 text-lg">
        <div v-if="availableHints > 0">
          Are you sure you want to use a hint? You have
          {{ availableHints }} hints left.
        </div>
        <div v-else>You have no more hints left.</div>
      </div>
    </div>
  </Modal>
  <Modal
    ref="resetWordModalRef"
    @close="closeModal('resetWord')"
    @confirm="getNewWord"
    showConfirm
  >
    <div
      class="flex flex-col items-center justify-center max-w-md gap-4 p-4 mx-auto font-sans text-center w-fit"
    >
      <div class="mt-4 text-lg">
        <div>Are you sure you want to get a new word?</div>
      </div>
    </div>
  </Modal>
  <Modal ref="modalRef" @close="closeModal">
    <div
      class="flex flex-col items-center justify-center max-w-md gap-4 p-4 mx-auto font-sans text-center w-fit"
    >
      <div class="mt-4 text-lg font-semibold">
        <div>
          {{ gameMessage }}
        </div>
      </div>
      <div
        v-if="definitions.length"
        class="flex flex-col justify-start gap-2 p-2 mt-2 font-sans rounded-md text-start bg-zinc-100"
      >
        <ol>
          <li
            v-for="(definition, index) in definitions"
            :key="`definition_${index}`"
            class="flex flex-col gap-2 mb-8"
          >
            <span class="font-mono"> - {{ definition.definition }}</span>
            <span class="text-gray-500">{{ definition.example }}</span>
            <div v-if="definition.synonyms.length">
              <span class="font-bold">Synonyms:</span>
              <span>{{ definition.synonyms.join(', ') }}</span>
            </div>
          </li>
        </ol>
      </div>
    </div>
  </Modal>
  <div
    class="flex flex-col items-center justify-between w-full h-screen max-w-xl gap-2 py-2 mx-auto font-sans text-center"
  >
    <div class="flex flex-col justify-between w-full h-full">
      <div class="flex flex-col items-center gap-2">
        <img src="/logo.png" class="w-40" />
        <div class="flex flex-col items-center gap-1">
          <div
            v-for="(row, rowIndex) in guesses"
            :key="rowIndex"
            class="flex flex-row gap-2 w-fit"
          >
            <div
              v-for="(letter, colIndex) in row"
              :key="colIndex"
              :class="[
                'w-12 h-12 rounded-lg flex items-center justify-center text-xl font-bold border-2',
                getLetterClass(rowIndex, colIndex),
              ]"
            >
              {{ letter }}
            </div>
          </div>
        </div>
      </div>
      <div
        class="bottom-0 flex flex-col items-center justify-center w-full gap-2"
      >
        <div class="flex flex-row gap-2">
          <button>
            <Icon
              @click="openModal('hint')"
              icon="ic:round-question-mark"
              height="42"
              class="transition-all duration-300 rounded-md text-violet-600 lg:text-violet-400 hover:text-violet-600"
            />
          </button>
          <div class="relative w-full p-2">
            <input
              v-model="currentGuess"
              maxlength="5"
              placeholder="Type your guess"
              inputmode="none"
              @keyup.enter="submitGuess"
              :disabled="gameOver"
              class="w-full p-2 text-lg text-center border rounded-md disabled:bg-gray-300"
              ref="inputRef"
              autofocus
            />
            <span
              class="absolute text-sm text-gray-500 transform -translate-y-1/2 right-4 top-1/2"
            >
              {{ currentGuess.length }}/5
            </span>
          </div>
          <button>
            <Icon
              @click="openModal('resetWord')"
              icon="ic:round-refresh"
              height="42"
              class="transition-all duration-300 rounded-md text-violet-600 lg:text-violet-400 hover:text-violet-600"
            />
          </button>
        </div>
        <Keyboard
          :correct-letters="correctLetters"
          :present-letters="presentLetters"
          :wrong-letters="wrongLetters"
          @update="updateWord"
          ref="keyboardRef"
        />
        <div class="w-full px-2">
          <button
            @click="submitGuess"
            :disabled="gameOver"
            class="w-full py-2 font-bold text-white transition-all duration-300 bg-blue-600 rounded-md hover:bg-blue-800 disabled:bg-gray-400"
          >
            Submit
          </button>
        </div>
        <small class="text-zinc-400"
          >Developed by
          <a
            href="https://github.com/lory1508"
            target="_blank"
            class="text-purple-600 underline underline-offset-2"
            >Lorenzo Galassi</a
          ></small
        >
      </div>
    </div>
  </div>
</template>

<script setup>
import Keyboard from '~/components/Keyboard.vue'
import Modal from '~/components/Modal.vue'
import { Icon } from '@iconify/vue'

const WORD_LENGTH = 5
const API_DEFINITIONS = 'https://api.dictionaryapi.dev/api/v2/entries/en/'
const API_WORD_GENERATOR = 'https://random-word-api.vercel.app/api'
const MAX_HINTS = 3

const wordToGuess = ref('')
const guesses = ref([...Array(6)].map(() => Array(5).fill('')))
const currentGuess = ref('')
const guessIndex = ref(0)
const gameMessage = ref('')
const gameOver = ref(false)
const definitions = ref([])
const invalidGuess = ref(false)
const correctLetters = ref([])
const wrongLetters = ref([])
const presentLetters = ref([])
const availableHints = ref(MAX_HINTS)

// Refs
const keyboardRef = ref(null)
const inputRef = ref(null)
const modalRef = ref(null)
const hintModalRef = ref(null)
const resetWordModalRef = ref(null)

watch(
  () => currentGuess.value,
  value => {
    if (invalidGuess.value) {
      invalidGuess.value = false
      gameMessage.value = ''
    }
  }
)

watch(
  () => gameOver.value,
  async () => {
    try {
      if (gameOver.value) {
        const response = await fetch(`${API_DEFINITIONS}${wordToGuess.value}`)
        const data = await response.json()
        definitions.value = data[0].meanings[0].definitions.slice(0, 3)
        openModal()
      }
    } catch (error) {
      console.error(error)
    }
  }
)

watch(
  () => guesses.value,
  newGuesses => {
    correctLetters.value = []
    presentLetters.value = []
    wrongLetters.value = []

    newGuesses.forEach(guess => {
      guess.forEach((letter, index) => {
        if (letter) {
          if (letter === wordToGuess.value[index]) {
            correctLetters.value.push(letter)
            const presentIndex = presentLetters.value.indexOf(letter)
            if (presentIndex !== -1) {
              presentLetters.value.splice(presentIndex, 1)
            }
          } else if (
            wordToGuess.value.includes(letter) &&
            !correctLetters.value.includes(letter)
          ) {
            if (!presentLetters.value.includes(letter)) {
              presentLetters.value.push(letter)
            }
          } else if (
            !correctLetters.value.includes(letter) &&
            !presentLetters.value.includes(letter)
          ) {
            if (!wrongLetters.value.includes(letter)) {
              wrongLetters.value.push(letter)
            }
          }
        }
      })
    })
  },
  {
    deep: true,
  }
)

const closeModal = modalToBeClosed => {
  switch (modalToBeClosed) {
    case 'hint':
      hintModalRef.value.closeModal()
      break

    case 'resetWord':
      resetWordModalRef.value.closeModal()
      break

    default:
      modalRef.value.closeModal()
      break
  }
  inputRef.value.focus()
  definitions.value = []
}

const openModal = modalToBeOpened => {
  switch (modalToBeOpened) {
    case 'hint':
      hintModalRef.value.openModal()
      break

    case 'resetWord':
      resetWordModalRef.value.openModal()
      break

    default:
      modalRef.value.openModal()
      break
  }
}

const updateWord = word => {
  currentGuess.value = word
}

const submitGuess = async () => {
  if (currentGuess.value.length !== 5 || gameOver.value) return

  try {
    const response = await fetch(`${API_DEFINITIONS}${currentGuess.value}`)
    const data = await response.json()
    if (!data.length) {
      invalidGuess.value = true
      openModal()
      gameMessage.value = '🤔 This word is not in the dictionary. Try again!'
      inputRef.value.focus()
      return
    }

    const guessArray = currentGuess.value.toUpperCase().split('')
    guesses.value[guessIndex.value] = [...guessArray]

    if (currentGuess.value.toUpperCase() === wordToGuess.value) {
      gameMessage.value = '🎉 Congratulations! You guessed the word!'
      gameOver.value = true
    } else if (guessIndex.value === 5) {
      gameMessage.value = `😞 Game over! The word was ${wordToGuess.value}.`
      gameOver.value = true
      openModal()
    }

    guessIndex.value++
    currentGuess.value = ''
    keyboardRef.value.word = ''
    inputRef.value.focus()
  } catch (error) {
    console.error(error)
  }
}

const getLetterClass = (row, col) => {
  const letter = guesses.value[row][col]
  if (!letter) {
    return 'border-gray-300'
  }
  if (letter === wordToGuess.value[col]) {
    return 'bg-green-500 text-white border-green-500'
  }
  if (wordToGuess.value.includes(letter)) {
    return 'bg-yellow-500 text-white border-yellow-500'
  }
  return 'bg-gray-500 text-white border-gray-500'
}

const showOneLetter = () => {
  closeModal('hint')
  if (availableHints.value === 0) return
  const availableLetters = wordToGuess.value
    .split('')
    .filter(l => !correctLetters.value.includes(l))
  const randomIndex = Math.floor(Math.random() * availableLetters.length)
  const randomLetter = availableLetters[randomIndex]
  const indexInWordToGuess = wordToGuess.value.indexOf(randomLetter)
  guesses.value[guessIndex.value][randomIndex] =
    wordToGuess.value[indexInWordToGuess]
  availableHints.value--
}

const getNewWord = async () => {
  try {
    closeModal('resetWord')
    correctLetters.value = []
    presentLetters.value = []
    wrongLetters.value = []
    guesses.value = [...Array(6)].map(() => Array(5).fill(''))
    guessIndex.value = 0
    gameOver.value = false
    availableHints.value = MAX_HINTS

    const response = await fetch(
      `${API_WORD_GENERATOR}?words=1&type=uppercase&length=${WORD_LENGTH}`
    )
    const data = await response.json()
    wordToGuess.value = data[0]
  } catch (error) {
    console.error(error)
  }
}

onMounted(async () => {
  await getNewWord()
})
</script>
