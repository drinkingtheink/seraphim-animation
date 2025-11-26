<template>
  <div v-if="isVisible" class="prophecy-message">
    {{ currentProphecy }}
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, defineEmits, defineExpose } from 'vue';

const prophecies = [
  'The watcher sees all, yet seeks to be seen...',
  'Five eyes witness the dance of light and shadow',
  'In the space between blinks, worlds are born',
  'The wings remember what the earth has forgotten',
  'You have gazed long enough. Now I gaze back.',
  'Time flows differently when observed by five',
  'The colors you see are echoes of ancient songs',
  'Between each feather lies a forgotten dream',
  'I am the guardian of the spaces in between',
  'What you call chaos, I call the original pattern'
];

const isVisible = ref(false);
const isSpeaking = ref(false);
const prophecyIndex = ref(0);
const currentProphecy = ref('');

// Define emits for parent to react to
const emit = defineEmits(['speak', 'speakEnd']);

const speakProphecy = () => {
  if (isSpeaking.value) return;
  
  isSpeaking.value = true;
  isVisible.value = true;
  currentProphecy.value = prophecies[prophecyIndex.value % prophecies.length];
  
  // Emit event so parent can trigger effects (eye blinks, symbols, etc.)
  emit('speak');
  
  // Hide after 4 seconds
  setTimeout(() => {
    isVisible.value = false;
    isSpeaking.value = false;
    emit('speakEnd');
  }, 4000);
  
  prophecyIndex.value++;
};

const handleKeydown = (e) => {
  if (e.code === 'Space' && !isSpeaking.value) {
    e.preventDefault();
    speakProphecy();
  }
};

onMounted(() => {
  window.addEventListener('keydown', handleKeydown);
});

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown);
});

// Expose method in case you want to trigger it programmatically
defineExpose({
  speakProphecy
});
</script>

<style scoped>
.prophecy-message {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-family: 'Georgia', serif;
  font-size: 32px;
  color: white;
  text-align: center;
  text-shadow: 
    10px 10px 10px rgba(0, 0, 0, 0.9);
  padding: 40px;
  max-width: 80%;
  pointer-events: none;
  z-index: 1000;
  animation: prophecy-appear 4s ease-in-out forwards;
  padding: 20px 40px;
  border-radius: 50%;
  background-color: rgba(0, 0, 0, 0.5);
  border: 1px solid white;
  backdrop-filter: hue-rotate(200deg);
}

@keyframes prophecy-appear {
  0% { 
    opacity: 0; 
    transform: translate(-50%, -50%) scale(0.5); 
  }
  10% { 
    opacity: 1; 
    transform: translate(-50%, -50%) scale(1.1); 
  }
  90% { 
    opacity: 1; 
    transform: translate(-50%, -50%) scale(1); 
  }
  100% { 
    opacity: 0; 
    transform: translate(-50%, -50%) scale(0.8); 
  }
}
</style>
