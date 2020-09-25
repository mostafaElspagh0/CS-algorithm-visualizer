<template>
  <div clss="haha">
    <div class="container">
      <div class="grid">
        <div
          v-for="i in cellsCount"
          class="cell"
          :key="i"
          :id="i"
          v-bind:style="{ 'background-color': graph[i] }"
        ></div>
      </div>
    </div>
  </div>
  <button @click="getPaths" :disabled="buttonDisabled">start</button>
</template>

<script lang="ts">
import { defineComponent } from "vue";

enum cellState {
  empty = "#FFFFFF",
  block = "#44464a",
  start = "#fffb08",
  end = "#081cff",
  visited = "#ff4400",
  current = "#ff7700",
  finalPath = "#00ff62"
}

class Queue<T> {
  private storage: T[] = [];

  constructor(private capacity: number = Infinity) {}

  enqueue(item: T): void {
    if (this.size() === this.capacity) {
      throw Error("Queue has reached max capacity, you cannot add more items");
    }
    this.storage.push(item);
  }
  dequeue(): T | undefined {
    return this.storage.shift();
  }
  size(): number {
    return this.storage.length;
  }
  isEmpty(): boolean {
    return this.storage.length == 0;
  }
}
export default defineComponent({
  name: "grid",
  props: {
    rows: Number,
    cols: Number
  },
  data() {
    if (this.rows == undefined || this.cols == undefined) return { graph: [] };

    const randomIntFromInterval: () => number = () => {
      if (this.rows == undefined || this.cols == undefined) return 0;
      const min = 1;
      const max = this.rows * this.cols;
      return Math.floor(Math.random() * (max - min + 1) + min);
    };

    const gfg: cellState[] = new Array(this.rows * this.cols + 20);
    for (let i = 0; i <= this.rows * this.cols; i++) {
      gfg[i] = cellState.empty;
    }
    for (
      let index = 0;
      index < Math.floor(this.rows * this.cols * 0.2);
      index++
    ) {
      gfg[randomIntFromInterval()] = cellState.block;
    }
    let startCell = -1,
      endCell = -1;
    while (startCell == -1) {
      const temp = randomIntFromInterval();
      if (gfg[temp] == cellState.empty) {
        startCell = temp;
        gfg[temp] = cellState.start;
      }
    }
    while (endCell == -1) {
      const temp = randomIntFromInterval();
      if (gfg[temp] == cellState.empty) {
        endCell = temp;
        gfg[temp] = cellState.end;
      }
    }
    return {
      graph: gfg,
      startCell: startCell,
      endCell: endCell,
      buttonDisabled: false
    };
  },
  computed: {
    cellsCount(): number {
      if (this.rows == undefined || this.cols == undefined) return 100;
      return this.rows * this.cols;
    }
  },
  methods: {
    async getPaths(): Promise<void> {
      if (
        this.cols == undefined ||
        this.rows == undefined ||
        this.startCell == undefined
      )
        return;
      const path: number[] = [];
      const parent: number[][] = new Array(this.cellsCount);
      this.buttonDisabled = true;
      const idToRC: (id: number) => [number, number] = (id: number) => {
        if (this.cols == undefined) throw new Error("");
        return [
          Math.floor((id - 1) / this.cols) + 1,
          ((id - 1) % this.cols) + 1
        ];
      };
      const rcToId = (row: number, col: number) => {
        if (this.cols == undefined) throw new Error("");
        return (row - 1) * this.cols + col;
      };
      const adjs = (node: number) => {
        if (this.cols == undefined || this.rows == undefined)
          throw new Error("");

        const [r, c] = idToRC(node);
        const dx = [1, 0, -1, 0];
        const dy = [0, 1, 0, -1];
        const ret: number[] = [];
        for (let i = 0; i < 4; i++) {
          if (
            r + dx[i] >= 1 &&
            r + dx[i] <= this.rows &&
            c + dy[i] >= 1 &&
            c + dy[i] <= this.cols
          ) {
            ret.push(rcToId(r + dx[i], c + dy[i]));
          }
        }
        console.log(ret);
        return ret;
      };
      const bfs = async () => {
        if (this.startCell == undefined) return;
        const dist = new Array(this.cellsCount + 10);
        dist.fill(this.cellsCount * 100);
        const q = new Queue<number>();
        q.enqueue(this.startCell);
        parent[this.startCell] = [-1];
        dist[this.startCell] = 0;
        while (!q.isEmpty()) {
          await new Promise(r => setTimeout(r, 200));
          const u = q.dequeue();
          if (u == undefined) break;

          if (this.graph[u] != cellState.start)
            this.graph[u] = cellState.visited;
          for (const v of adjs(u)) {
            if (
              this.graph[v] == cellState.block ||
              this.graph[v] == cellState.visited
            )
              continue;
            if (dist[v] > dist[u] + 1) {
              dist[v] = dist[u] + 1;
              q.enqueue(v);

              parent[v] = [];
              parent[v].push(u);
              if (this.graph[v] == cellState.end) return;
              this.graph[v] = cellState.current;
            } else if (dist[v] == dist[u] + 1) {
              parent[v].push(u);
            }
          }
        }
      };
      let pathFound = false;
      const finalPath: number[] = [];
      const findpaths = (u: number) => {
        if (pathFound) return;
        if (u == -1) {
          for (const i of path) {
            finalPath.push(i);
          }
          pathFound = true;
          return;
        }
        if (parent[u] == undefined) return;
        for (const par of parent[u]) {
          path.push(u);
          findpaths(par);
          path.pop();
        }
      };
      bfs().then(async () => {
        if (this.endCell == undefined) throw new Error("");
        findpaths(this.endCell);
        for (const i of finalPath) {
          await new Promise(r => setTimeout(r, 200));
          if (
            this.graph[i] != cellState.start &&
            this.graph[i] != cellState.end
          )
            this.graph[i] = cellState.finalPath;
        }
        this.buttonDisabled = false;
      });
    }
  }
});
</script>

<style scoped vars="{ rows , cols }">
button {
  position: fixed;
  bottom: 10px;
  right: 10px;
  height: fit-content;
  width: fit-content;
}
.container {
  background: black;
  display: inline-block;
  border: 1px solid black;
  margin: auto;
}
.grid {
  display: grid;
  height: 75vh;
  width: 75vw;
  grid-template-columns: repeat(
    var(--cols),
    calc((100% - (1px * (var(--cols) - 1))) / var(--cols))
  );
  grid-template-rows: repeat(
    var(--rows),
    calc((100% - (1px * (var(--rows) - 1))) / var(--rows))
  );
  grid-gap: 1px;
}
.cell {
  -moz-user-select: none;
  user-select: none;
  -webkit-user-select: none;
  justify-content: center;
  align-items: center;
  background: white;
  text-align: center;
  align-items: center;
  justify-content: center;
  display: flex;
}
</style>
