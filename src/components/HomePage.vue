<template>
  <div id="app" class="container mx-auto px-4 py-8">
    <h1 class="text-2xl font-bold mb-4">Your Lists</h1>
    <div v-for="todo in todos" :key="todo.id" class="mb-8 border border-gray-300 rounded p-4">
      <div class="flex justify-between items-center">
        <h2 class="text-xl font-semibold cursor-pointer" @click="toggleTodo(todo.id)">
          {{ todo.name }}
          <span v-if="todo.completed" class="text-gray-400">(Completed)</span>
        </h2>
        <div>
          <button @click="editTodoName(todo.id)" class="ml-2 mr-4 text-blue-500 hover:text-blue-700">
            Edit
          </button>
          <button @click="removeTodo(todo.id)" class="text-red-500 hover:text-red-700">
            Remove
          </button>
        </div>
      </div>
      <ul class="list-none ml-4">
        <li v-for="task in todo.tasks" :key="task.id" class="flex items-center mb-2">
          <input type="checkbox" v-model="task.completed" class="mr-2 focus:ring-blue-500 focus:ring-opacity-50">
          <span v-if="!task.completed" class="text-gray-700">{{ task.name }}</span>
          <span v-else class="text-gray-400 line-through">{{ task.name }}</span>
        </li>
        <li class="flex items-center">
          <input v-model="todoNames[todo.id]" type="text" class="border border-gray-300 rounded px-2 py-1 w-full focus:ring-blue-500 focus:ring-opacity-50" placeholder="Add new task">
          <button @click="addTask(todo.id)" class="ml-2 bg-blue-500 hover:bg-blue-700 text-white font-semibold py-1 px-2 rounded">Add</button>
        </li>
      </ul>
    </div>
    <button @click="addTodo" class="bg-green-500 hover:bg-green-700 text-white font-semibold py-2 px-4 rounded">Add New Todo List</button>
  </div>
</template>

<script setup>
  import { ref } from 'vue';

  const todos = ref([
    { id: 1, name: 'Office', tasks: [{ id: 11, name: 'Buy coffee', completed: false }, { id: 12, name: 'Fix code', completed: true }] },
    { id: 2, name: 'Home', tasks: [{ id: 21, name: 'Buy grocery', completed: false }, { id: 22, name: 'Fix chair', completed: false }] },
  ]);

  let todoNames = ref([]);

  function toggleTodo(id) {
    const todo = todos.value.find(todo => todo.id === id);
    if (todo) {
      todo.completed = !todo.completed;
    }
  }

  function removeTodo(id) {
    todos.value = todos.value.filter(todo => todo.id !== id);
  }

  function addTask(todoId) {
  const taskName = todoNames.value[todoId].trim();
  if (taskName) { // Check if name exists and has content
    const todo = todos.value.find(todo => todo.id === todoId);
    if (todo) {
      todo.tasks.push({ id: Date.now(), name: taskName, completed: false }); // Use the taskName entered in the input field
      todoNames.value[todoId] = ''; // Reset the input field after adding the task
    } else {
      console.warn(`Todo list with id ${todoId} not found. Cannot add task.`);
    }
  }
}

function addTodo() {
  const todoName = prompt("Enter the name of the todo list:");
  if (todoName) { // Check if the user entered a name
    const newTodo = {
      id: Date.now(),
      name: todoName,
      tasks: [],
      completed: false,
    };
    todos.value.push(newTodo);
    todoNames.value.push(''); // Add an empty string for the new todo list name
  }
}

function editTodoName(todoId) {
  const newName = prompt("Enter the new name for the todo list:");
  if (newName) {
    const todo = todos.value.find(todo => todo.id === todoId);
    if (todo) {
      todo.name = newName;
      // todoNames.value[todoId] = newName;
    } 
    else {
      console.warn(`Todo list with id ${todoId} not found. Cannot edit name.`);
    }
  }
}

</script>