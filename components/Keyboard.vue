<template>
  <div class="flex flex-col w-full">
    <div class="flex flex-row justify-center">
      <div 
        v-for="(key, index) in firstRow" 
        :key="`firstRow_${index}`" 
        class="flex flex-col items-center justify-center w-12 h-12 m-1 font-semibold border border-gray-300 rounded-md cursor-pointer"
        :class="{
          'bg-green-200 text-green-600': correctLetters.includes(key),
          'bg-amber-200 text-amber-600': presentLetters.includes(key),
          'bg-zinc-200 text-zinc-600': wrongLetters.includes(key),
        }"
        @click="addLetter(key)"
      >
        <span>
          {{ key }}
        </span>
      </div>
    </div>
    <div class="flex flex-row justify-center">
      <div 
        v-for="(key, index) in secondRow" 
        :key="`secondRow_${index}`" 
        class="flex flex-col items-center justify-center w-12 h-12 m-1 font-semibold border border-gray-300 rounded-md cursor-pointer"
        :class="{
        'bg-green-200 text-green-600': correctLetters.includes(key),
        'bg-amber-200 text-amber-600': presentLetters.includes(key),
        'bg-zinc-200 text-zinc-600': wrongLetters.includes(key),
        }"
        @click="addLetter(key)"
      >
        <span>
          {{ key }}
        </span>
      </div>
    </div>
    <div class="flex flex-row justify-center">
      <div 
        v-for="(key, index) in thirdRow" 
        :key="`thirdRow_${index}`" 
        class="flex flex-col items-center justify-center w-12 h-12 m-1 font-semibold border border-gray-300 rounded-md cursor-pointer"
        :class="{
        'bg-green-200 text-green-600': correctLetters.includes(key),
        'bg-amber-200 text-amber-600': presentLetters.includes(key),
        'bg-zinc-200 text-zinc-600': wrongLetters.includes(key),
        }"
        @click="addLetter(key)"
      >
        <span>
          {{ key }}
        </span>
      </div>
      <div
        class="flex flex-col items-center justify-center w-24 h-12 m-1 font-semibold border border-gray-300 rounded-md cursor-pointer"
        @click="addLetter('{backspace}')"
      >
        <Icon icon="ic:round-backspace" height="24" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue';

const props = defineProps({
  correctLetters: {
    type: Array,
    default: () => []
  },
  wrongLetters: {
    type: Array,
    default: () => []
  },
  presentLetters: {
    type: Array,
    default: () => []
  }
});
const emit = defineEmits(["update"]);

const firstRow = ["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"];
const secondRow = ["A", "S", "D", "F", "G", "H", "J", "K", "L"];
const thirdRow = ["Z", "X", "C", "V", "B", "N", "M"];

const word = ref("");

const addLetter = (letter) => {
  if (letter === "{backspace}") {
    word.value = word.value.slice(0, -1);
  } else {
    if(word.value.length >= 5) return;
    word.value += letter;
  }
  emit("update", word.value);
};

defineExpose({ word });
</script>

<style>
.correct {
  @apply !bg-green-500 !text-white;
}

.present {
  @apply !bg-yellow-500 !text-white;
}

.wrong {
  @apply !bg-gray-700 !text-white;
}
</style>