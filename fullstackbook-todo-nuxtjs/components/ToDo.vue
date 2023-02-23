<template>
  <div class="todoRow">
    <input type="checkbox" v-model="todo.completed" @click="$emit('onChange', todo)" />
    <input class="todoInput" v-model="todo.name" @input="$emit('onChange', todo)" />
    <button class="deleteBtn" @click="$emit('onDelete', todo)"><img
        src="material-symbols_delete-outline-sharp.svg" alt="delete" /></button>
  </div>
</template>

<script setup>
const props = defineProps(['todo'])
</script>

<style>
.todoInput {
  padding: 5px;
  border: 1px solid black;
  width: 194px;
  height: 40px;
  margin: 5px;
}
.todoRow {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin: 5px 20px;
}
.deleteBtn {
  background: none;
  border: 0;
  cursor: pointer;
}
</style>