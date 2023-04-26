<template>
  <div id="app" :class="darknessClass">
    <nav>
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link> |
      <toggle :text="isDarkMode ? 'Dark mode' : 'Light mode'" @swap="toggleDarkMode" :checked="isDarkMode" class="justify-content-start"/>
    </nav>
    <router-view/>
  </div>
</template>

<script>
import Toggle from '@/components/Toggle.vue';

export default {
  name: "AppPage",
  data() {
    return {
      isDarkMode: false,
    }
  },
  components: {
    Toggle
  },
  created() {
    this.isDarkMode = localStorage.getItem('dark') === "true";
  },
  methods: {
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode;
      localStorage.setItem('dark', this.isDarkMode);
    }
  },
  computed: {
    darknessClass() {
      return this.isDarkMode ? 'dark' : 'light';
    }
  }
}
</script>

<style>
body:has(> .dark) {
  background: black;
}

.dark label, .dark i, .dark h1, .dark h3, .dark pre, .dark h5{
  color: white;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

.dark nav a {
  font-weight: bold;
  color: white;
}
nav a.router-link-exact-active {
  color: #42b983;
}
</style>
