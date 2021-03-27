<template>
  <Clicker />
</template>

<script>
import Clicker from './components/Clicker.vue';

export default {
  name: 'App',
  components: {
    Clicker,
  },
};
</script>

<style lang="scss">
#app {
  background-color: black;
  border: 1px solid white;
}

body {
  margin: 0;
  overflow: hidden;
}
</style>
