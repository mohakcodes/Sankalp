<template>
  <div class="w-full flex justify-center items-center h-[70vh]">
    <div class="w-[80%] md:w-[35%] flex flex-col justify-center items-center space-y-4">
      <h1 class="text-2xl font-bold text-left p-2">Login To Your Account</h1>
      <input
        type="email"
        v-model="email"
        placeholder="Enter Your E-Mail"
        class="w-full px-4 py-2 border-2 border-black"
      />
      <input
        type="text"
        v-model="password"
        placeholder="Enter Your Password"
        class="w-full px-4 py-2 border-2 border-black"
      />
      <button @click="handleLogin" class="w-full p-2 text-md font-bold bg-emerald-400 rounded-lg hover:bg-black hover:text-emerald-400">
        LOGIN
      </button>
      <div v-if="error" class="text-red-500 text-sm">Something Went Wrong</div>
      <div class="w-full flex justify-start items-center space-x-2">
        <p class="italic">Not Registered?</p>
        <p class="font-semibold px-4 py-[4px] bg-slate-400 hover:bg-fuchsia-300 rounded-lg">
          <a href="/auth/signup">SignUp</a>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { API_URL } from '../url.js';

export default {
  data() {
    return {
      email: "",
      password: "",
      error: false,
    };
  },
  methods: {
    async handleLogin() {
      if (!this.email || !this.password) {
        this.error = true;
        return;
      }

      try {
        const response = await axios.post(`${API_URL}/api/auth/login`, {
          email: this.email,
          password: this.password,
        },{
          withCredentials: true,
        });

        console.log('Login response:', response);
        this.resetForm();
        window.location.href = '/';
      } 
      catch (err) {
        console.error('Login error:', err);
        this.error = true;
      }
    },
    resetForm() {
      this.email = "";
      this.password = "";
      this.error = false;
    },
  },
};
</script>