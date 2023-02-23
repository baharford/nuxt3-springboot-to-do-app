<template>
  <div class="container">
    <div class="mainInputContainer">
      <input class="mainInput" placeholder="What needs to be done?" v-model="newTodoText" @keyup.enter="handleAddToDo" />
    </div>
    <p class="m20" v-if="pending">Loading...</p>
    <div v-else>
      <div class="toDoRow" v-for="todo of todos" :key="todo.id">
        <ToDo :todo="todo" @onChange="handleChange" @onDelete="handleDelete" />
      </div>
    </div>
    <div class="filters">
      <button class="filterBtn" :class="{ filterActive: filter === undefined }"
        @click="handleFilterChange()">All</button>
      <button class="filterBtn" :class="{ filterActive: filter === false }"
        @click="handleFilterChange(false)">Active</button>
      <button class="filterBtn" :class="{ filterActive: filter === true }"
        @click="handleFilterChange(true)">Completed</button>
    </div>
  </div>
</template>

<script setup>
const config = useRuntimeConfig();
const newTodoText = ref('');
const filter = ref(undefined);

// get todos from db using filter
const { pending, data: todos } = await useAsyncData(
  'todos',
  () => $fetch(`${config.apiURL}/todos`, { 
    params: {
      completed: filter.value,
    }
  }), {
    // if filter ref changes , then reload
    watch: [
      filter,
    ],
    ssr: false, // set to try if you want to enable server-side rendering
  }
)

const handleAddToDo = async () => {
  const todo = { name: newTodoText.value, completed: false };
  const res = await fetch(`${config.apiURL}/todos`, {
    method: "POST",
    body: JSON.stringify(todo),
    headers: {
      'Content-Type': 'application/json'
    }
  })
  .catch((e) => { 
    console.log(e);
  });
  const json = await res.json();
  todos.value.push(json);
  newTodoText.value = ''
}

const handleChange = async (todo) => {
  todo.completed = !todo.completed
  await fetch(`${config.apiURL}/todos/${todo.id}`, {
    method: "PUT",
    body: JSON.stringify(todo),
    headers: {
      'Content-Type': 'application/json'
    }
  });
}

const handleDelete = async (todo) => {
  await fetch(`${config.apiURL}/todos/${todo.id}`, {
    method: "DELETE"
  });
  const idx = todos.value.findIndex((t) => t.id === todo.id)
  const copy = [...todos.value]
  copy.splice(idx, 1)
  todos.value = copy
}

const handleFilterChange = (value) => {
  filter.value = value
}
  
</script>

<style>
.container {
  width: 300px;
  border: 1px solid black;
}

.mainInputContainer {
  width: 100%;
  margin: 20px 0;
}

.mainInput {
  padding: 5px;
  border: 1px solid black;
  margin: auto;
  display: block;
  width: 260px;
  height: 40px;
}

.filters {
  display: flex;
  justify-content: space-between;
  padding: 20px;
  margin-top: 20px;
  border-top: 1px solid black;
}

.filterBtn {
  background: none;
  border: none;
  cursor: pointer;
}

.filterActive {
  font-weight: bold;
  text-decoration: underline;
}

.m20 {
  margin: 20px;
}
</style>
