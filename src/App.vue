<template>
  <MemoryGame heading="Memory Game" buttonText="New Game"/>
</template>

<script>
import MemoryGame from './components/memory-game.vue';

export default {
  name: 'App',
  components: {
    MemoryGame
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 50px;
}
</style>
