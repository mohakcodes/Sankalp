<template>
    <div class="w-full flex justify-center items-center h-[70vh]">
      <div class="w-[80%] md:w-[35%] flex flex-col justify-center items-center space-y-4">
        <h1 class="text-2xl font-bold text-left p-2">Create Your Account</h1>
  
        <div v-if="error" class="text-red-500 text-sm mb-4">
          Something Went Wrong
        </div>
  
        <input
          v-model="username"
          type="text"
          placeholder="Enter Username"
          class="w-full px-4 py-2 border-2 border-black"
        />
        <input
          v-model="email"
          type="email"
          placeholder="Enter Your E-Mail"
          class="w-full px-4 py-2 border-2 border-black"
        />
        <input
          v-model="password"
          type="password"
          placeholder="Enter Your Password"
          class="w-full px-4 py-2 border-2 border-black"
        />
  
        <button @click="handleSubmit" class="w-full p-2 text-md font-bold bg-emerald-400 rounded-lg hover:bg-black hover:text-emerald-400">
          SIGN UP
        </button>
  
        <div class="w-full flex justify-start items-center space-x-2">
          <p class="italic">Already Registered?</p>
          <p class="font-semibold px-4 py-[4px] bg-slate-400 hover:bg-fuchsia-300 rounded-lg">
            <a href="/auth/login">Login</a>
          </p>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { API_URL } from '../url.js';
  import axios from 'axios';
  
  export default {
    data() {
      return {
        username: '',
        email: '',
        password: '',
        error: false,
      };
    },
    methods: {
      isEmailValid(email) {
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        return emailRegex.test(email);
      },
      async handleSubmit() {
        if (!this.username || !this.email || !this.password) {
          this.error = true;
          return;
        }
        if (!this.isEmailValid(this.email)) {
          this.error = true;
          return;
        }
  
        try {
          const res = await axios.post(`${API_URL}/api/auth/signup`, {
            username: this.username,
            email: this.email,
            password: this.password,
          });
  
          console.log('res->', res);
          this.resetForm();
          window.location.href = '/auth/login';
        } 
        catch (err) {
          this.error = true;
          console.error(err);
        }
      },
      resetForm() {
        this.username = '';
        this.email = '';
        this.password = '';
        this.error = false;
      },
    },
  };
  </script>
  