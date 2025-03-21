<template>
  <div class="flex flex-col">
    <div class="flex flex-row">
      <div 
        v-for="(key, index) in firstRow" 
        :key="`firstRow_${index}`" 
        class="flex flex-col items-center justify-center w-12 h-12 m-1 border border-gray-300 rounded-md cursor-pointer"
        @click="addLetter(key)"
      >
        {{ key }}
      </div>
    </div>
    <div class="flex flex-row justify-center">
      <div 
        v-for="(key, index) in secondRow" 
        :key="`secondRow_${index}`" 
        class="flex flex-col items-center justify-center w-12 h-12 m-1 border border-gray-300 rounded-md cursor-pointer"
        @click="addLetter(key)"
      >
        {{ key }}
      </div>
    </div>
    <div class="flex flex-row justify-center">
      <div 
        v-for="(key, index) in thirdRow" 
        :key="`thirdRow_${index}`" 
        class="flex flex-col items-center justify-center w-12 h-12 m-1 border border-gray-300 rounded-md cursor-pointer"
        @click="addLetter(key)"
      >
        <span v-if="key != '{backspace}'">
          {{ key }}
        </span>
        <Icon v-else icon="ic:round-backspace" height="24" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue';

const emit = defineEmits(["update"]);

const firstRow = ["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"];
const secondRow = ["A", "S", "D", "F", "G", "H", "J", "K", "L"];
const thirdRow = ["Z", "X", "C", "V", "B", "N", "M", "{backspace}"];

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