<template>
  <div 
    class="flex flex-row justify-center gap-2"
  >
    <LetterCard 
      v-for="(letter, i) in word"
      :key="`letter-${i}`"
      :letter="guessedWordArray[i]" 
      :status="status[i]"
      :active="active"
    />
  </div>
</template>

<script setup>
const props = defineProps({
  word: {
    type: Array,
    required: true,
  },
  guessedWord: {
    type: String,
    required: true,
  },
  status: {
    type: Array,
    default: () => [],
  },
  active: {
    type: Boolean,
    default: false,
  }
})

const guessedWordArray = ref([])
watch(
  () => props.guessedWord,
  (newValue) => {
    guessedWordArray.value = props.guessedWord.toUpperCase().split('')
  }
)

</script>