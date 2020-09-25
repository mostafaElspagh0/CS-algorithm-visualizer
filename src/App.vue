<template>
  <Grid :rows="30" :cols="30"></Grid>
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
#app {
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
