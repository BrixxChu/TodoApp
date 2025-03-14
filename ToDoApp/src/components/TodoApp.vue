<script setup>
import { ref } from "vue";

const newTodo = ref("");
const todos = ref([]);
const error = ref("");
const editId = ref(null);
const editedText = ref("");

const addTodo = () => {
  if (!newTodo.value.trim()) {
    error.value = "To-do cannot be empty";
    return;
  }
  todos.value.push({
    id: Date.now(),
    text: newTodo.value,
    timestamp: new Date().toLocaleString(),
  });
  newTodo.value = "";
  error.value = "";
};

const startEditing = (todo) => {
  editId.value = todo.id;
  editedText.value = todo.text;
};

const saveTodo = (todo) => {
  if (!editedText.value.trim()) {
    editId.value = null;
    return;
  }

  const index = todos.value.findIndex((t) => t.id === todo.id);
  if (index !== -1) {
    todos.value[index].text = editedText.value;
  }

  editId.value = null;
};

const removeTodo = (id) => {
  todos.value = todos.value.filter((todo) => todo.id !== id);
};
</script>

<template>
  <main
    class="flex flex-col items-center justify-center min-h-screen w-full px-4 bg-black text-white"
  >
    <div class="w-full max-w-[500px] p-6 rounded-lg">
      <img
        src="/public/phCollablogo.jpg"
        alt="logo"
        class="mb-10 border border-white rounded-md w-[500px]"
      />
      <label for="todoInput" class="text-white mb-3 font-bold text-3xl"
        >TODO</label
      >

      <div class="grid grid-cols-12 gap-3 mt-3">
        <div class="col-span-8">
          <input
            id="todoInput"
            v-model="newTodo"
            placeholder="Enter your todo here"
            class="w-full h-[40px] text-black px-2 rounded-md"
          />
        </div>
        <div class="col-span-4 flex items-end">
          <button
            @click="addTodo"
            class="h-[40px] w-full bg-white text-black rounded-sm"
          >
            Add Todo
          </button>
        </div>
      </div>

      <p v-if="error" class="text-red-500 mt-2">{{ error }}</p>

      <div
        class="mt-4 border border-white flex flex-col p-2 max-h-[350px] overflow-auto"
      >
        <div v-show="todos.length === 0" class="text-center">
          Nothing to display
        </div>
        <div
          v-for="(todo, index) in todos"
          :key="todo.id"
          class="p-2 border-b-2 border-white flex flex-col items-start"
        >
          <div class="text-xs font-bold text-white">TODO #{{ index + 1 }}</div>

          <button
            @click="removeTodo(todo.id)"
            class="text-white text-lg self-end mt-2"
          >
            ✖
          </button>
          <div class="mb-2 text-sm flex flex-row w-full">
            <p
              v-if="editId !== todo.id"
              @click="startEditing(todo)"
              class="break-all mb-3 w-full cursor-pointer"
            >
              {{ todo.text }}
            </p>

            <input
              v-else
              v-model="editedText"
              @blur="saveTodo(todo)"
              @keyup.enter="saveTodo(todo)"
              class="w-full px-2 py-1 text-black rounded-md"
              autofocus
            />
          </div>
          <div v-show="editId === todo.id" class="text-center">
            Please write an update
          </div>
          <div class="text-xs font-bold tracking-widest self-end">
            {{ todo.timestamp }}
          </div>
        </div>
      </div>
    </div>
  </main>
</template>
