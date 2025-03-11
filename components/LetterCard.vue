<template>
  <div 
    class="border-2 text-center pt-1 rounded-lg w-14 h-16 custom-font"
    :class="acceptedStatuses[props.status]"  
  >
    <span 
      v-if="status == 'correct'"
      class="text-5xl"
    >
      {{ letter }}
    </span>
  </div>
</template>

<script setup>
const props = defineProps({
  letter: String,
  status: {
    type: String,
    default: 'default',
    validator: (value) => {
      return ['correct', 'wrongPlace', 'default'].includes(value)
    },
  }
})

const acceptedStatuses = {
  correct: 'bg-green-300 text-green-800 border-green-200',
  wrongPlace: 'bg-yellow-300 text-yellow-800 border-yellow-200',
  default: 'bg-zinc-300 text-zinc-800 border-zinc-200',
}
</script>

<style>
.custom-font {
  font-family: "Delius Unicase", cursive;
}
</style>