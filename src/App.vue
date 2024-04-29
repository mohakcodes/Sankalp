<script setup>
import {ref, computed} from 'vue';
import HeaderNav from './components/Header-Nav.vue';
import HomePage from './components/HomePage.vue';
// import AboutPage from './components/AboutPage.vue';
import LoginPage from './components/LoginPage.vue';
import SignupPage from './components/SignupPage.vue';

const routes = [
  { path: '/', component: HomePage },
  // { path: '/about', component: AboutPage },
  { path: '/login', component: LoginPage },
  { path: '/signup', component: SignupPage },
];

const currentPath = ref(window.location.pathname); // Use pathname for path-based routing

window.addEventListener('popstate', () => {
  currentPath.value = window.location.pathname;
});

const currentView = computed(() => {
  const matchingRoute = routes.find(route => route.path === currentPath.value);
  return matchingRoute ? matchingRoute.component : HomePage;
});
</script>

<template>
  <div id="app">
    <HeaderNav :routes="routes" />
    <component :is="currentView" />
  </div>
</template>