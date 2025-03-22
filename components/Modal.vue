<template>
  <div
    v-if="open"
    class="fixed inset-0 z-50 flex items-center justify-center px-8 bg-black bg-opacity-50"
  >
    <div class="flex flex-col p-4 bg-white rounded-lg w-96">
      <slot />

      <div class="flex flex-col justify-center lg:flex-row lg:gap-4">
        <button
          @click="emit('close')"
          class="px-4 py-2 mt-4 text-white transition-all duration-300 bg-red-500 rounded-md hover:bg-red-700"
        >
          Close
        </button>
        <button
          v-if="showConfirm"
          @click="emit('confirm')"
          class="px-4 py-2 mt-4 text-white transition-all duration-300 bg-blue-500 rounded-md hover:bg-blue-700"
        >
          Confirm
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  showConfirm: {
    type: Boolean,
    default: false,
  },
})
const emit = defineEmits(['close', 'confirm'])

const open = ref(false)

const openModal = () => {
  open.value = true
}

const closeModal = () => {
  open.value = false
}

defineExpose({
  openModal,
  closeModal,
})
</script>
