<template>
  <div class="flex flex-col gap-4 items-center">
    <div 
      v-for="i in 6"
      :key="`word-${i}`"
      class="flex flex-row gap-2 justify-center"
    >
      <LetterCard :letter="word[0]" />
      <LetterCard :letter="word[1]" status="wrongPlace" />
      <LetterCard :letter="word[2]" />
      <LetterCard :letter="word[3]" status="correct" />
      <LetterCard :letter="word[4]" />
    </div>
  </div>
</template>

<script setup>
const mockWord = 'peace'
const word = ref([])

onMounted(() => {
  word.value = mockWord.toUpperCase().split('')
})
</script>

<style>

</style>