<template>
  <div 
    v-if="active"
    class="h-16 pt-1 text-center border-2 rounded-lg w-14 custom-font"
    :class="acceptedStatuses[props.status]"  
  >
    <span 
      class="text-5xl"
    >
      {{ letter }}
    </span>
  </div>
  <div 
    v-else
    class="h-16 pt-1 text-center border-2 rounded-lg w-14 custom-font bg-zinc-300 text-zinc-800 border-zinc-200"
  >  
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
  },
  active: {
    type: Boolean,
    default: false,
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
  font-family: "Outfit", sans-serif;
}
</style>