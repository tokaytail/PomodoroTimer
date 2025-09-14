<script setup>
import { inject } from "vue";

const emit = defineEmits(["close"]);

const focusDuration = inject("focusDuration");
const breakDuration = inject("breakDuration")
const totalSessions = inject("totalSessions");
const isDarkMode = inject("isDarkMode");

function handleClose() {
    emit("close");
}
</script>

<template>
    <div id="timer-settings-overlay" class="fixed inset-0 z-40" @click="handleClose"></div>

    <!-- TODO: save all settings in localStorage -->
    <div id="timer-settings-panel" class="fixed top-1/2 left-1/2 z-50 
        w-full max-w-md -translate-x-1/2 -translate-y-1/2 
        flex flex-col justify-center items-center
        rounded-md p-6 shadow-lg dark:shadow-zinc-900
        bg-zinc-50 text-zinc-950
        dark:bg-zinc-950 dark:text-zinc-50">
        <h1 class="text-xl capitalize">settings</h1>
        <div id="timer-settings-grid" class="w-full h-full grid grid-cols-2 gap-x-4 gap-y-5 items-center pt-6">
            <label for="focus-input" class="block text-sm font-medium uppercase text-zinc-500">Focus (minutes)</label>
            <input type="number" id="focus-input" v-model="focusDuration" class="block w-full 
            rounded-md border-zinc-300 dark:border-zinc-700 bg-zinc-100 dark:bg-zinc-900
            p-2 focus:border-lime-400 focus:ring focus:ring-lime-300">

            <label for="break-input" class="block text-sm font-medium uppercase text-zinc-500">Break (minutes)</label>
            <input type="number" id="break-input" v-model="breakDuration" class="block w-full
            rounded-md border-zinc-300 dark:border-zinc-700 bg-zinc-100 dark:bg-zinc-900
            p-2 focus:border-lime-400 focus:ring focus:ring-lime-300">

            <!-- TODO: add limitations 0 < input <= 24 -->
            <label for="sessions-input" class="block text-sm font-medium uppercase text-zinc-500">Sessions</label>
            <input type="number" id="sessions-input" v-model="totalSessions" class="block w-full
            rounded-md border-zinc-300 dark:border-zinc-700 bg-zinc-100 dark:bg-zinc-900
            p-2 focus:border-lime-400 focus:ring focus:ring-lime-300">

            <hr class="col-span-2 border-zinc-200 dark:border-zinc-700 my-2">

            <label for="toggle-theme" class="block text-sm font-medium uppercase text-zinc-500">
                Toggle dark theme
            </label>
            <input id="toggle-theme" type="checkbox" v-model="isDarkMode" class="sr-only peer" checked>
            <label for="toggle-theme" class="relative w-11 h-6 bg-gray-200 rounded-full 
            peer peer-focus:ring-4 peer-focus:ring-lime-300 dark:peer-focus:ring-lime-800 
            dark:bg-zinc-700 peer-checked:after:translate-x-full rtl:peer-checked:after:-translate-x-full 
            peer-checked:after:border-zinc-50 after:content-[''] after:absolute after:top-0.5 after:start-[2px] 
            after:bg-zinc-50 after:border-zinc-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all 
            dark:border-zinc-600 peer-checked:bg-lime-600 dark:peer-checked:bg-lime-600 cursor-pointer">
            </label>
        </div>
    </div>
</template>