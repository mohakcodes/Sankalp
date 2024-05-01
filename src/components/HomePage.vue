<template>
  <div id="app" class="container h-[90vh] mx-auto px-4 py-8 bg-gray-200">
    <h1 v-if="isLoggedIn" class="text-2xl font-bold text-center mb-8">Your Lists</h1>
    <div v-else class="flex h-[80vh] items-center justify-center">
      <div class="text-center px-4 py-8 bg-white rounded shadow-md"> <p class="text-2xl text-red-600 font-bold mb-4">
          You are not logged in.
        </p>
        <p class="text-lg text-gray-700">Please <a href="/auth/login" class="text-blue-800 font-bold underline">login</a> first to manage your lists</p>
      </div>
    </div>
    

    <template v-if="isLoggedIn">
      <button @click="addList" class="w-[35%] px-2 flex justify-center mx-auto bg-red-600 hover:bg-red-700 text-white font-semibold my-3 py-2 rounded">Add New List</button>
      <div v-for="list in userLists" :key="list._id" class="mb-8 bg-yellow-300 border-2 border-black rounded shadow-md p-4">
        <div class="flex justify-between items-center">
          <h2 class="text-xl mb-3 font-bold cursor-pointer">
            {{ list.title }}
          </h2>
          <div class="flex space-x-2">
            <button @click="editListName(list._id)" class="bg-green-500 hover:bg-green-700 text-white font-semibold rounded-lg px-3 py-1 focus:outline-none">
              Edit
            </button>
            <button @click="removeList(list._id)" class="bg-red-500 hover:bg-red-700 text-white font-semibold rounded-lg px-3 py-1 focus:outline-none">
              Remove
            </button>
          </div>
        </div>
        <ul class="list-none ml-4">
          <li v-for="(task, index) in list.tasks" :key="task._id" class="flex items-center mb-2 w-[85%]">
            <input type="checkbox" v-model="task.completed" @change="updateTaskCompletion(list._id, index)" class="mr-2 focus:ring-blue-500 focus:ring-opacity-50">
            <span v-if="!task.completed" class="text-gray-700 font-bold pl-2">{{ task.description }}</span>
            <span v-else class="text-gray-600 font-semibold line-through pl-2">{{ task.description }}</span>
            <div class="flex justify-end ml-auto">
              <button @click="removeTask(list._id, index)" class="text-red-600 font-bold hover:text-red-700 focus:outline-none">
                Remove
              </button>
            </div>
          </li>
          <li class="flex items-center">
            <input v-model="newTasks[list._id]" type="text" class="border border-gray-300 rounded px-2 py-1 w-full focus:ring-blue-500 focus:ring-opacity-50" placeholder="Add new task">
            <button @click="addTask(list._id)" class="ml-2 bg-blue-500 hover:bg-blue-700 text-white font-semibold py-1 px-2 rounded">Add</button>
          </li>
        </ul>
      </div>
    </template>
  </div>
</template>



<script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  import { API_URL } from '../url.js';

  const userLists = ref([]);
  const isLoggedIn = ref(false);
  const userData = ref(null);
  const newTasks = ref({});

  const getUser = async() => {
    try {
      const response = await axios.get(`${API_URL}/api/auth/refresh`, { withCredentials: true });
      console.log("response",response.data);
      userData.value = response.data;
      if(userData.value){
        isLoggedIn.value = true;
      }
    } catch (error) {
      console.error('Error fetching user data:', error);
    }
  }

  onMounted(async () => {
    await getUser();
    await fetchUserLists();
  });

  async function fetchUserLists() {
    try {
      console.log("userData",userData.value._id);
      const response = await axios.get(`${API_URL}/api/list/${userData.value._id}`,{ withCredentials: true });
      console.log("All Lists Fetched",response.data.data);
      userLists.value = response.data.data;
    } 
    catch (error) {
      console.error('Error fetching todo lists:', error);
    }
  }

  async function addList() {
    const listName = prompt("Enter the name of the todo list:");
    if(!listName){
      return;
    }
    try {
      const response = await axios.post(`${API_URL}/api/list`,{ title:listName, userId:userData.value._id },{ withCredentials: true });
      console.log("Current List Added",response.data.data);
      await fetchUserLists();
    } 
    catch (error) {
      console.error('Error creating todo:', error);
    }
  }

  async function editListName(listId) {
    const newName = prompt("Enter the new name for the todo list:");
    if (newName) {
      try {
        const response = await axios.put(`${API_URL}/api/list/${listId}`,{ title:newName },{ withCredentials: true });
        console.log("List Name Updated",response.data.data);
        await fetchUserLists();
      } 
      catch (error) {
        console.error('Error updating todo name:', error);
      }
    }
  }

  async function removeList(listId) {
    try {
      const response = await axios.delete(`${API_URL}/api/list/${listId}`,{ withCredentials: true });
      console.log("List Deleted",response.data.data);
      await fetchUserLists();
    } 
    catch (error) {
      console.error('Error deleting todo:', error);
    }
  }

  async function addTask(listId) {
    const newTask = newTasks.value[listId];
    if (!newTask) {
      return;
    }
    try {
      const response = await axios.post(`${API_URL}/api/task`,{ description:newTask, listId },{ withCredentials: true });
      console.log("Task Added",response.data.data);
      await fetchUserLists();
    } 
    catch (error) {
      console.error('Error adding task:', error);
    }
    newTasks.value[listId] = '';
  }

  async function updateTaskCompletion(listId, index){
    console.log(listId,index);
    try {
      const response = await axios.put(`${API_URL}/api/task/toggle`,{listId,index} ,{ withCredentials: true });
      console.log("Task Updated",response.data.data);
      await fetchUserLists();
    } 
    catch (error) {
      console.error('Error updating task:', error);
    }
  }

  async function removeTask(listId,index){
    try {
      const response = await axios.delete(`${API_URL}/api/task/${listId}/${index}`,{ withCredentials: true });
      console.log("Task Deleted",response.data.data);
      await fetchUserLists();
    } 
    catch (error) {
      console.error('Error deleting task:', error);
    }
  }
</script>