<script setup>
import { isComputedPropertyName } from 'typescript';
import Logo from '../assets/geckodoro_logo.svg'
import { ref, computed, onMounted, onUnmounted, resolveDynamicComponent } from 'vue';
import { Input } from 'postcss';

const FOCUS = "focus";
const BREAK = "break";

const stage = ref(FOCUS);
const focusDurationInMinutes = ref(25);
const breakDurantionInMinutes = ref(5);
const timerState = ref(null);
const currentStageInSeconds = ref(0);
const totalSessions = ref(4);
const currentSession = ref(1);

// TODO: end session audio on/off option
// const endSessionAudio = new Audio('path_to_the_audio');
const startButtonText = computed(() => {
  return timerState.value ? "pause" : "start";
});

const displayHours = computed(() => {
  const h = Math.floor(currentStageInSeconds.value / 3600);
  return String(h).padStart(2, '0');
});

const displayMinutes = computed(() => {
  const m = Math.floor((currentStageInSeconds.value % 3600) / 60);
  return String(m).padStart(2, '0');
});

const displaySeconds = computed(() => {
  const s = Math.floor(currentStageInSeconds.value % 60);
  return String(s).padStart(2, '0');
});

function pauseTimer() {
  if (timerState.value) {
    clearInterval(timerState.value);
    timerState.value = null;
  }
}

function setTimer() {
  if (stage.value === FOCUS) {
    currentStageInSeconds.value = focusDurationInMinutes.value * 60;
  }
  else currentStageInSeconds.value = breakDurantionInMinutes.value * 60;
}

function updateTimer() {
  currentStageInSeconds.value--;

  if (currentStageInSeconds.value === 0) {
    // sound the audio
  }

  if (currentStageInSeconds.value < 0) {
    changeStage();
  }
}

function changeStage() {
  pauseTimer();

  if (stage.value === FOCUS) {
    stage.value = BREAK;
  } else {
    stage.value = FOCUS;
    currentSession.value++;

    if (currentSession.value > totalSessions.value) {
      currentSession.value = 1;
    }
  }

  setTimer();
  // TODO: session auto-start option
  // toggleStartStop();
}

function toggleStartStop() {
  if (timerState.value) {
    pauseTimer();
  }
  else {
    if (currentStageInSeconds.value <= 0) {
      setTimer();
    }
    timerState.value = setInterval(updateTimer, 1000);
  }
}

function resetTimer() {
  pauseTimer();
  setTimer();
}

function openSettings() {
  alert("Aqui você abriria um modal para alterar 'focusDurationInMinutes' e 'breakDurationInMinutes'. Como eles são 'refs', qualquer mudança neles será refletida automaticamente.");
}

function handleKeydown(event) {
  switch (event.key) {
    case ' ':
      event.preventDefault();
      toggleStartStop();
      break;

    case 'r':
    case 'R':
      resetTimer();
      break;

    case 's':
    case 'S':
      openSettings();
      break;
  }
}

onMounted(() => {
  setTimer();
  window.addEventListener("keydown", handleKeydown);
});

onUnmounted(() => {
  window.removeEventListener("keydown", handleKeydown);
  pauseTimer();
})
</script>

<template>
  <div id="timer-container" class="flex flex-col w-2/3 h-2/3 justify-center items-center gap-y-4">
    <!-- <img :src="Logo" alt="Geckodoro Logo" class="w-3 2 h-32"> -->

    <div id="timer-session-ring"
      class="flex flex-col w-60 h-60 justify-center items-center rounded-full border-8 border-zinc-800">
      <div id="timer-session-icon" class="h-1/10">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
          class="size-6">
          <path stroke-linecap="round" stroke-linejoin="round"
            d="M2.036 12.322a1.012 1.012 0 0 1 0-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178Z" />
          <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
        </svg>
      </div>

      <div id="timer-digits-section" class="flex flex-row h-1/3 justify-around text-5xl">
        <span id="timer-digits-hours" class="flex w-full h-full justify-center items-center">
          {{ displayHours }}
        </span>
        <span id="timer-digit-separator" class="flex w-full h-full justify-center items-center">:</span>
        <span id="timer-digits-minutes" class="flex w-full h-full justify-center items-center">
          {{ displayMinutes }}
        </span>
        <!-- TODO: no seconds option -->
        <span id="timer-digit-separator" class="flex w-full h-full justify-center items-center">:</span>
        <span id="timer-digits-seconds" class="flex w-full h-full justify-center items-center">
          {{ displaySeconds }}
        </span>
      </div>

      <div id="timer-session-markers" class="flex flex-row w-10 h-1/8 justify-between items-center">
        <div v-for="n in totalSessions" :key="n" class="w-1 h-5 rounded-md"
          :class="n <= currentSession ? 'bg-lime-400' : 'bg-zinc-800'"></div>
      </div>

      <div id="timer-stage-section" class="flex flex-row justify-center items-center text-base text-zinc-700 uppercase">
        <span id="timer-stage-text">{{ stage }}</span>
      </div>
    </div>

    <div id="controls-section" class="flex flex-row justify-between items-center gap-x-4">
      <button type="button" id="reset-button"
        class="w-10 h-10 rounded-full flex justify-center items-center border border-zinc-800 cursor-pointer"
        @click="resetTimer">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
          class="size-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M9 15 3 9m0 0 6-6M3 9h12a6 6 0 0 1 0 12h-3" />
        </svg>
      </button>
      <button type="button" id="start-stop-button"
        class="w-20 h-12 bg-zinc-900 rounded-2xl justify-center items-center border border-zinc-800 uppercase cursor-pointer"
        @click="toggleStartStop">
        {{ startButtonText }}
      </button>
      <button type="button" id="settings-button"
        class="w-10 h-10 rounded-full flex justify-center items-center border border-zinc-800 cursor-pointer"
        @click="openSettings">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"
          class="size-6">
          <path stroke-linecap="round" stroke-linejoin="round"
            d="M9.594 3.94c.09-.542.56-.94 1.11-.94h2.593c.55 0 1.02.398 1.11.94l.213 1.281c.063.374.313.686.645.87.074.04.147.083.22.127.325.196.72.257 1.075.124l1.217-.456a1.125 1.125 0 0 1 1.37.49l1.296 2.247a1.125 1.125 0 0 1-.26 1.431l-1.003.827c-.293.241-.438.613-.43.992a7.723 7.723 0 0 1 0 .255c-.008.378.137.75.43.991l1.004.827c.424.35.534.955.26 1.43l-1.298 2.247a1.125 1.125 0 0 1-1.369.491l-1.217-.456c-.355-.133-.75-.072-1.076.124a6.47 6.47 0 0 1-.22.128c-.331.183-.581.495-.644.869l-.213 1.281c-.09.543-.56.94-1.11.94h-2.594c-.55 0-1.019-.398-1.11-.94l-.213-1.281c-.062-.374-.312-.686-.644-.87a6.52 6.52 0 0 1-.22-.127c-.325-.196-.72-.257-1.076-.124l-1.217.456a1.125 1.125 0 0 1-1.369-.49l-1.297-2.247a1.125 1.125 0 0 1 .26-1.431l1.004-.827c.292-.24.437-.613.43-.991a6.932 6.932 0 0 1 0-.255c.007-.38-.138-.751-.43-.992l-1.004-.827a1.125 1.125 0 0 1-.26-1.43l1.297-2.247a1.125 1.125 0 0 1 1.37-.491l1.216.456c.356.133.751.072 1.076-.124.072-.044.146-.086.22-.128.332-.183.582-.495.644-.869l.214-1.28Z" />
          <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
        </svg>
      </button>
    </div>
  </div>
</template>
