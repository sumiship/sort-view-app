<template>
  <div id="app">
    <div class="view-box">
      <div class="col" :style="col_style(index)" v-for="(value, index) in source" :key="index">
        <!-- <span>{{ format_number(value) }}</span> -->
        <div class="cell" :style="cell_style(value, SourceLengh - n)" v-for="n of SourceLengh" :key="n"></div>
      </div>
    </div>
    <div class="control">
      <div class="button" @click="source_random(source)">random</div>
      <div class="button" @click="selection_sort(source)">selectionSort</div>
      <div class="button" @click="insertion_sort(source)">insertionSort</div>
      <div class="button" @click="bubble_sort(source)">bubbleSort</div>
      <div class="button" @click="quick_sort(source, 0, SourceLengh - 1)">quickSort</div>
    </div>
    <div class="range">
      speed:{{ 501 - updateSpeed }}<br />
      <input type="range" max="500" min="1" v-model="updateSpeed" />
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
  SourceLengh = 80;
  updateSpeed = 70;

  format_number(num: number): string {
    if (num < 10) return `0${num}`;
    return String(num);
  }

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

  async quick_sort(arr: number[], begin: number, end: number): Promise<void> {
    if (begin >= end) return;
    const pivot = arr[begin];

    this.checking = [];
    for (let t = begin; t <= end; t++) {
      this.checking.push(t);
    }
    let check = 0;
    let i = begin;
    let j = end;
    this.searching = [i, j];
    // console.log("------------");
    // console.log(begin, end);
    // console.log(JSON.parse(JSON.stringify(arr)));

    while (i <= j) {
      while (i <= end && arr[i] < pivot) {
        i++;
        await this.update_searching([i, j]);
      }
      while (i >= begin && arr[j] >= pivot) {
        j--;
        await this.update_searching([i, j]);
      }
      // console.log(`check${i},${j}`);
      if (i > j) break;
      // console.log(`swap${i},${j}`);
      check++;
      this.swap(i++, j--, arr);
      await this.update_source(arr);
    }
    // console.log(i, j);
    // console.log(JSON.parse(JSON.stringify(arr)));
    await this.quick_sort(arr, begin, i - 1);
    if (check == 0) i++;
    await this.quick_sort(arr, i, end);
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

  mounted(): void {
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
  font-size: 1rem;
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
.control {
  display: flex;
}
.button {
  text-align: center;
  font-size: 2rem;
  border: 1px solid red;
  width: 100%;
}
.button:hover {
  background-color: pink;
}
.range {
  font-size: 4rem;
}
.range input {
  width: 50%;
}
</style>
