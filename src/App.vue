
<script setup lang="ts">
import { onMounted, ref } from "vue";
import type {Cell, Vector2D} from "@/Types.ts";
const canvas = ref(null);
const CELL_SIZE = 10;
const CANVAS_SIZE_H = CELL_SIZE * 80;
const CANVAS_SIZE_W = CELL_SIZE * 100;
const COLS = CELL_SIZE * 80;
const ROWS = CELL_SIZE * 100;
let timer: number
const filled = ref<Cell[]>([]);

const grid = ref<boolean[][]>(
    Array.from({ length: ROWS }, () => Array.from({ length: COLS }, () => false))
);

const mousePos = ref();


function ctx2d() {
  const ctx = canvas.value?.getContext("2d");
  if (!ctx) {
   throw new Error("no canvas context");
  }
  return ctx;
}

function clear() {
  const ctx = ctx2d();
  ctx.clearRect(0, 0, ctx.canvas.width, ctx.canvas.height);
}

function draw() {
  const ctx = ctx2d();

  clear();

  ctx.fillStyle = "#34343";
  for (const cell of filled.value) {
    const pos:Vector2D = cell.pos
    ctx.fillRect(pos.x * CELL_SIZE, pos.y * CELL_SIZE, CELL_SIZE, CELL_SIZE);
  }
}


function update(){
  for (const cell of filled.value) {

  }
}


onMounted(() => {
  filled.value.push({x:0,y:0});
  filled.value.push({x:0,y:COLS -1});
  filled.value.push({x:ROWS -1,y:0});
  filled.value.push({x:ROWS -1,y:COLS -1});
  console.log(filled.value);
  draw();
  timer = window.setInterval(() => {
    update();
    draw();
  }, 1000 / 30); //
});
</script>





<template>
  <header>

  </header>
  <div style="display:grid">
    <canvas
        ref="canvas"
        :width="CANVAS_SIZE_W"
        :height="CANVAS_SIZE_H"
    />

  </div>
</template>


<style scoped>


</style>
