<template>

<div class="p-6 max-w-lg mx-auto bg-black text-yellow-400 shadow-xl rounded-xl border border-yellow-400">
  <h1 class="text-3xl font-bold mb-4 text-center text-yellow-400">⚡ Lista zadań</h1>

  <h2 class="text-lg mb-4 text-center">
    Do zrobienia: <span class="font-semibold">{{ tasksToDo }}</span> |
    Zrobione: <span class="font-semibold">{{ tasksDone }}</span>
  </h2>

  <!-- Input + button -->
  <div class="flex gap-2 mb-4">
    <input
      v-model="newTask"
      @keyup.enter="addTask"
      type="text"
      placeholder="Dodaj zadanie..."
      class="bg-black border border-yellow-400 rounded-lg p-2 flex-1 text-yellow-300 placeholder-yellow-600 focus:outline-none focus:ring-2 focus:ring-yellow-400"
    />
    <button
      @click="addTask"
      class="bg-yellow-400 text-black font-semibold px-4 py-2 rounded-lg hover:bg-yellow-300 transition"
    >
      Dodaj
    </button>
  </div>

  <!-- Usuuń wszystkie zrobione -->
  <div class="flex justify-end mb-4">
    <button
      @click="deleteCompleted"
      class="bg-yellow-500 text-black font-semibold px-4 py-2 rounded-lg hover:bg-yellow-400 transition disabled:opacity-40 disabled:cursor-not-allowed"
      :disabled="tasksDone === 0"
    >
      Usuń wszystkie zrobione
    </button>
  </div>



  <!-- Lista zadań -->
  <TransitionGroup name="list" tag="ul" class="space-y-2">
    <li
      v-for="(task, index) in tasks"
      :key="task.id"
      class="flex justify-between items-center p-3 rounded-lg border transition hover:shadow-md"
      :class="task.done ? 'bg-yellow-900/40 border-yellow-700' : 'bg-black border-yellow-600'"
    >
      <span
        :class="[
          'task-text transition',
          task.done ? 'line-through text-yellow-700 italic' : 'text-yellow-300'
        ]"
      >
        {{ task.text }}
      </span>
      <div class="flex gap-2">
        <button
          @click="toggleTask(index)"
          class="bg-yellow-400 text-black px-2 py-1 rounded hover:bg-yellow-300 transition font-bold"
        >
          ✔
        </button>
        <button
          @click="deleteTask(index)"
          class="bg-black border border-yellow-400 text-yellow-400 px-2 py-1 rounded hover:bg-yellow-700 hover:text-black transition font-bold"
        >
          ✖
        </button>
      </div>
    </li>
  </TransitionGroup>
</div>


</template>

<script setup>


import { ref, watch, onMounted } from 'vue'

import { computed } from 'vue'

const tasksToDo = computed(() => tasks.value.filter(t => !t.done).length)
const tasksDone = computed(() => tasks.value.filter(t => t.done).length)


const newTask = ref('')
const tasks = ref([])

let idCounter = 0

// Wczytanie zadań z localStorage przy starcie
onMounted(() => {
  const savedTasks = localStorage.getItem('tasks')
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks)
    // ustaw idCounter na wartość większą od najwyższego id
    idCounter = tasks.value.length ? Math.max(...tasks.value.map(t => t.id)) + 1 : 0
  }
})

// Watch – zapis do localStorage przy każdej zmianie
watch(
  tasks,
  (newVal) => {
    localStorage.setItem('tasks', JSON.stringify(newVal))
  },
  { deep: true }
)

const addTask = () => {
  if (newTask.value.trim() !== '') {
    tasks.value.push({ id: idCounter++, text: newTask.value, done: false })
    newTask.value = ''
  }
}

const toggleTask = (index) => {
  tasks.value = tasks.value.map((task, i) =>
    i === index ? { ...task, done: !task.done } : task
  )
}

const deleteTask = (index) => {
  tasks.value.splice(index, 1)
}

const deleteCompleted = () => {
  tasks.value = tasks.value.filter(task => !task.done)
}




</script>



<style>
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}
.list-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}
.list-leave-to {
  opacity: 0;
  transform: translateY(10px);
}
</style>
