<template>
  <Grid :rows="5" :cols="10" />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Grid from "./components/Grid.vue";

export default defineComponent({
  name: "App",
  components: {
    Grid
  }
});
</script>

<style>
body {
  margin: 0px;
  padding: 0px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
