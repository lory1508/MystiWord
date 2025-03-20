<template>
  <div 
    :class="keyboardClass"
  ></div>
</template>

<script setup>
import Keyboard from "simple-keyboard";
import "simple-keyboard/build/css/index.css";

const props = defineProps( {
  keyboardClass: {
    default: "simple-keyboard",
    type: String
  },
  input: {
    type: String
  }
})
const emit = defineEmits(["onChange", "onKeyPress"]);

const keyboard = ref(null);

onMounted(() => {
  keyboard.value = new Keyboard(props.keyboardClass, {
    onChange: onChange,
    onKeyPress: onKeyPress,
    maxLength: 5,
    layout: {
      'default': [
        'q w e r t y u i o p',
        'a s d f g h j k l',
        '{bksp} z x c v b n m {enter}',
      ]
    },
    buttonTheme: [
      {
        class: "correct", // Green for correct position
        buttons: "A S D"  // Example letters, update dynamically
      },
      {
        class: "present", // Yellow for wrong position
        buttons: "E R T"
      },
      {
        class: "absent", // Dark grey for not in the word
        buttons: "Y U I"
      }
    ]
  });
})

const updateKeyboardColors = (letterStatuses) => {
  const buttonTheme = Object.entries(letterStatuses).map(([letter, status]) => ({
    class: status, // Assigns Tailwind classes dynamically
    buttons: letter.toUpperCase()
  }));

  keyboard.setOptions({ buttonTheme });
}

const letterClasses = {
  'correct': "bg-green-500 text-white",
  'wrong-place': "bg-yellow-500 text-white",
  'wrong': "bg-gray-700 text-white"
};


const onChange = (input) => {
  emit("onChange", input);
}

const onKeyPress = (button) => {
  emit("onKeyPress", button);

  /**
   * If you want to handle the shift and caps lock buttons
   */
  if (button === "{shift}" || button === "{lock}") handleShift();
}

const handleShift = () => {
  let currentLayout = keyboard.value.options.layoutName;
  let shiftToggle = currentLayout === "default" ? "shift" : "default";

  keyboard.value.setOptions({
    layoutName: shiftToggle
  });
}

watch(() => props.input, (input) => {
  keyboard.value.setInput(input);
})
</script>