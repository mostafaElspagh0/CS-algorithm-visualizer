<template>
  <div class="container">
    <div class="grid">
      <div
        v-for="i in cellsCount"
        class="cell"
        :key="i"
        :id="i"
        @click="increse(i)"
      >
        {{ graph[i] }}
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "grid",
  props: {
    rows: Number,
    cols: Number
  },
  data() {
    if (this.rows == undefined || this.cols == undefined) return { graph: [] };
    const gfg: number[] = new Array(this.rows * this.cols);
    for (let i = 0; i <= this.rows * this.cols; i++) {
      console.log("fsf");
      gfg[i] = 0;
    }
    return {
      graph: gfg
    };
  },
  computed: {
    cellsCount(): number {
      if (this.rows == undefined || this.cols == undefined) return -1;
      return this.rows * this.cols;
    }
  },
  methods: {
    increse(id: number): void {
      if (this.graph == undefined) return;

      this.graph[id] += 1;
    }
  }
});
</script>

<style scoped vars="{ rows , cols }">
.container {
  background: black;
  display: inline-block;
  border: 5px solid black;
  margin: auto;
}
.grid {
  display: grid;
  grid-template-columns: repeat(var(--cols), 80px);
  grid-template-rows: repeat(var(--rows), 80px);
  grid-gap: 5px;
}
.cell {
  -moz-user-select: none;
  user-select: none;
  -webkit-user-select: none;
  justify-content: center;
  align-items: center;
  display: flex;
  font-family: Arial;
  font-size: 3rem;
  font-weight: bold;
  background: white;
}
</style>
