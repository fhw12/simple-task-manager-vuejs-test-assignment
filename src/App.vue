<template>
  <header>
    <h1>Менеджер задач</h1>
    <span>
      Загрузить задачи:
      <input class="task-loader-button" type="file" accept=".json" @change="loadTasksFromFile">
    </span>
  </header>

  <main>
    <h2>Задачи</h2>
    <div class="task-container" v-if="tasks.length">
        <div v-for="task in tasks" :key="task.id">
          <label class="task">
            <h2>{{ task.title }}</h2>
            <input type="checkbox" v-model="task.done" @change="updateTasks">
          </label>
        </div>
    </div>
  </main>
</template>

<style scoped>
header {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  background-color: #fafafa;
  padding-block: 5px;
  padding-inline: 10px;
}

main {
  padding: 10px;
}

.task-loader-button, .task-done-button {
  border-style: none;
  border-radius: 5px;
  font-size: 16px;
  padding: 8px 16px;
  cursor: pointer;
}

.task-container {
  display: grid;
  grid-template-columns: 1fr;
  gap: 10px;
}

@media (min-width: 720px) {
  .task-container {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
}

.task {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  background-color: #dddddd;
  padding: 10px;
  cursor: pointer;
  user-select: none;
}
</style>

<script setup>
import { ref } from 'vue';

const tasks = ref([]);

const updateTasks = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
}

const loadTasksFromFile = (event) => {
  const input = event.target;
  const file = input.files?.[0];

  if (file == null) { return; }

  const reader = new FileReader();
  reader.onload = () => {
    try {
      const data = JSON.parse(reader.result);
      tasks.value = data;
      updateTasks();
    } catch (e) {
      console.log(`Error when loadTasksFromFile: ${e}`);
    }
  };
  reader.readAsText(file);
}

const loadTasksFromLocalStorage = () => {
  const tasksValue = localStorage.getItem('tasks');
  
  if(tasksValue == null) { return; }

  try {
    tasks.value = JSON.parse(tasksValue);
  } catch (e) {
    console.log(`Error when loadTasksFromLocalStorage: ${e}`);
  }
}

loadTasksFromLocalStorage();
</script>