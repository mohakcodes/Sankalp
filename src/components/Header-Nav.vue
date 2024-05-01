<template>
  <header class="flex justify-between items-center p-4 bg-yellow-400 border-b border-gray-300">
    <div class="flex-grow">
      <span class="text-3xl font-bold p-1 rounded-xl">Sankalp</span>
    </div>
    <nav class="space-x-4 font-semibold">
      <a href="/" class="text-gray-700 hover:text-gray-900 text-lg">Home</a>
      <a v-if="!isLoggedIn" href="/auth/login" class="text-gray-700 hover:text-gray-900 text-lg">Login</a>
      <a v-if="isLoggedIn" @click="handleLogout" class="text-gray-700 hover:text-gray-900 text-lg cursor-pointer">Logout</a>
    </nav>
  </header>
</template>

<script setup>

import { ref, onMounted } from 'vue';
import axios from 'axios';
import {API_URL} from '../url.js';

const userData = ref(null);
const isLoggedIn = ref(false);

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

  onMounted(getUser);

  const handleLogout = async() => {
    try {
      const res = await axios.get(`${API_URL}/api/auth/logout`, { withCredentials: true });
      console.log("res",res);
      userData.value = null;
      isLoggedIn.value = false;
      window.location.href = '/';
    } catch (error) {
      console.error('Error logging out:', error);
    }
  }

</script>

<style scoped>
</style>