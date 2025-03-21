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
  // },
  // correctLetters: {
  //   type: Array
  // },
  // presentLetters: {
  //   type: Array
  // },
  // wrongLetters: {
  //   type: Array
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
        'Q W E R T Y U I O P',
        'A S D F G H J K L',
        '{bksp} Z X C V B N M {enter}',
      ]
    }
  });
})

const updateKeyboardColors = (letterStatuses) => {
  const buttonTheme = Object.entries(letterStatuses).map(([letter, status]) => ({
    class: status, // Assigns Tailwind classes dynamically
    buttons: letter.toUpperCase()
  }));

  keyboard.setOptions({ buttonTheme });
}

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

const defineClasses = (correctLetters, presentLetters, wrongLetters) => {
  keyboard.value.addButtonTheme(correctLetters.join(" "), "correct");
  keyboard.value.addButtonTheme(presentLetters.join(" "), "present");
  keyboard.value.addButtonTheme(wrongLetters.join(" "), "wrong");
  console.log(keyboard.value.options);
}

watch(() => props.input, (input) => {
  keyboard.value.setInput(input);
})

defineExpose({
  defineClasses
})
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