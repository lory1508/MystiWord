<template>
  <div v-if="open" class="fixed inset-0 z-50 flex items-center justify-center px-8 bg-black bg-opacity-50">
    <div class="flex flex-col p-4 bg-white rounded-lg w-96">
      <slot />

      <button @click="emit('close')" class="px-4 py-2 mt-4 text-white bg-red-500 rounded-md">Close</button>
    </div>
  </div>
</template>

<script setup>
const emit = defineEmits(['close']);

const open = ref(false);

const openModal = () => {
  open.value = true;
};

const closeModal = () => {
  open.value = false;
};

// watch( () => open.value, async (value) => {
//   if (value) {
//     setTimeout(() => {
//       open.value = false;
//     }, 5000);
//   }
// });

defineExpose({
  openModal,
  closeModal
});
</script>