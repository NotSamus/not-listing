<script lang="ts">
  import { onMount } from 'svelte';

  let newTask = '';
  let tasks: { text: string; done: boolean }[] = [];
  const STORAGE_KEY = 'notsamus-tasks';

  // Save tasks array to localStorage
  function saveTasks() {
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(tasks));
      console.log('Tasks saved to localStorage');
    } catch (e) {
      console.error('Failed to save tasks:', e);
    }
  }

  function loadTasks() {
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    if (raw) {
      const parsed = JSON.parse(raw);
      tasks = parsed.map((task: { text: string; done: boolean }) => ({
        text: task.text,
        done: task.done
      }));
      console.log('Tasks loaded from localStorage:', tasks);
    }
  } catch (e) {
    console.error('Failed to load tasks:', e);
    tasks = [];
  }
}


  function addTask() {
    const trimmed = newTask.trim();
    if (!trimmed) return;
    tasks = [...tasks, { text: trimmed, done: false }];
    newTask = '';
    saveTasks();
  }

  function removeTask(index: number) {
    tasks.splice(index, 1);
    tasks = [...tasks];
    saveTasks();
  }

  onMount(loadTasks);
</script>


<h1>NotSamus To-Do List 📝</h1>

<input
  class="input-tasks"
  bind:value={newTask}
  placeholder="Add a new task"
  on:keydown={(e) => { if (e.key === 'Enter') addTask(); }}
/>
<button class="add-button" on:click={addTask}>Add</button>

<ul>
  {#each tasks as task, i}
    <li>
      <button class="delete-button" on:click={() => removeTask(i)}>🗑️</button>
      <span> {task.text}</span>
    </li>
  {/each}
</ul>

<style>
  :global(body) {
    font-family: sans-serif;
    padding: 20px;
    max-width: 400px;
    margin: auto;
  }
  /* Delete button functionality */
  .delete-button {
  background-color: #FFFFFF;
  border: none;
  border-radius: 8px;
  padding: 8px 14px;
  font-size: 16px;
  cursor: pointer;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
}

  .delete-button:hover {
    background-color: #a07cd2;
    transition: background-color 0.3s ease, box-shadow 0.2s ease;
  }
  /* add button functionality */
  .add-button:hover {
    background-color: #a07cd2;
    transition: background-color 0.3s ease, box-shadow 0.2s ease;
  }

  .add-button{
    background-color: #FFFFFF;
    color: black;
    border: none;
    border-radius: 8px;
    padding: 8px 14px;
    font-size: 14px;
    font-weight: 500;
    cursor: pointer;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  }
  /* Input tasks */
  .input-tasks {
    width: 75%;
    padding: 12px;
    margin-bottom: 4px;
    border: 1.5px solid #ccc;
    border-radius: 4px;
  }
  
  /* This is for the list */
  li {
  display: flex;
  align-items: center;
  gap: 12px; /* Add spacing between icon and text */
  background: #f7f7f7;
  padding: 12px 16px;
  margin-bottom: 10px;
  border-radius: 12px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}
li span {
  flex-grow: 1;
  font-size: 16px;
  padding-left: 4px;
  overflow-wrap: anywhere;
}

ul {
  list-style: none;
  padding: 0;
}

</style>
