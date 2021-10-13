<template>
  <div id="app">
    <div class="view-box">
      <div class="col" :style="col_style(index)" v-for="(value, index) in source" :key="index">
        <span>{{ value }}</span>
        <div class="cell" :style="cell_style(value, SourceLengh - n)" v-for="n of SourceLengh" :key="n"></div>
      </div>
    </div>
    <div class="control">
      <button @click="source_random(source)">random</button>
      <button @click="selection_sort(source)">selectionSort</button>
      <button @click="insertion_sort(source)">insertionSort</button>
      <button @click="bubble_sort(source)">bubbleSort</button>
      <input type="range" max="300" min="1" v-model="updateSpeed" />
      swapTimeout: {{ updateSpeed }}
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({})
export default class App extends Vue {
  source: number[] = [];
  searching: number[] = [];
  checking: number[] = [];
  SourceLengh = 40;
  updateSpeed = 70;

  col_style(index: number): string {
    return this.searching.indexOf(index) != -1 ? "background-color: orange" : this.checking.indexOf(index) != -1 ? "background-color: red" : "";
  }
  cell_style(value: number, cell: number): string {
    return value == cell ? "background-color: black" : "";
  }

  async update_source(arr: number[]): Promise<void> {
    return new Promise((resolve) => {
      this.source = JSON.parse(JSON.stringify(arr));
      setTimeout(resolve, this.updateSpeed);
    });
  }
  async update_searching(nums: number[]): Promise<void> {
    return new Promise((resolve) => {
      this.searching = nums;
      setTimeout(resolve, this.updateSpeed);
    });
  }

  private swap(id1: number, id2: number, arr: number[]): void {
    [arr[id1], arr[id2]] = [arr[id2], arr[id1]];
  }

  async selection_sort(arr: number[]): Promise<void> {
    for (let i = 0; i < arr.length - 1; i++) {
      this.checking = [i];
      const minElement = { index: i, value: arr[i] };
      for (let j = i + 1; j < arr.length; j++) {
        await this.update_searching([j]);
        if (minElement.value > arr[j]) {
          minElement.index = j;
          minElement.value = arr[j];
          this.checking = [j];
        }
      }
      this.swap(i, minElement.index, arr);
      await this.update_source(arr);
    }
    this.searching = [-1];
    this.checking = [-1];
  }

  async insertion_sort(arr: number[]): Promise<void> {
    for (let i = 1; i < arr.length; i++) {
      this.checking = [i];
      let j = i;
      for (; j > 0; j--) {
        await this.update_searching([j]);
        if (arr[i] >= arr[j - 1]) {
          break;
        }
      }
      arr.splice(j, 0, arr[i]);
      arr.splice(i + 1, 1);
      await this.update_source(arr);
    }
    this.searching = [-1];
    this.checking = [-1];
  }

  async bubble_sort(arr: number[]): Promise<void> {
    for (let i = 0; i < arr.length; i++) {
      for (let j = arr.length - 1; j > i; j--) {
        await this.update_searching([j, j - 1]);
        if (arr[j] < arr[j - 1]) {
          this.swap(j, j - 1, arr);
          await this.update_source(arr);
        }
      }
    }
    this.searching = [-1];
    this.checking = [-1];
  }
  async source_random(arr: number[]): Promise<void> {
    for (let i = arr.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      this.swap(i, j, arr);
      await this.update_source(arr);
    }
  }

  make_source(length: number): number[] {
    const source: number[] = [];
    for (let i = 0; i < length; i++) {
      source.push(i);
    }
    return source;
  }

  mounted() {
    this.source = this.make_source(this.SourceLengh);
    // console.log(this.source);
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
  font-size: 2rem;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.view-box {
  width: 90%;
  max-width: 80vh;
  margin: 50px auto 0;
  display: flex;
}
.col {
  width: 100%;
}
.cell {
  padding-top: 100%;
  border: 1px solid black;
}
</style>
