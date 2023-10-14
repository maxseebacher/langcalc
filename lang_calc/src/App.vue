<template>
  <side_bar />
  <footer class="footer">
    Maximilian Seebacher - 2023
  </footer>
</template>

<script>
import side_bar from './components/side_bar.vue';

export default {
  name: 'App',
  components: {
    side_bar
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.footer {
  left: 0;
  right: 0;
  position: fixed;
  bottom: 15px;
}
</style>
