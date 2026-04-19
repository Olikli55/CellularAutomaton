
<script setup lang="ts">
import { onMounted, ref } from "vue";
import type {Cell, Vector2} from "@/Types.ts";
const canvas = ref(null);
const CELL_SIZE = 10;
const COLS = 100;
const ROWS = 80;
const CANVAS_SIZE_H = CELL_SIZE * ROWS;
const CANVAS_SIZE_W = CELL_SIZE * COLS;

let timer: number

const filled = ref<Cell[]>([]);
const grid = ref<boolean[][]>([]);

function fillGrid() {
  grid.value = Array.from({ length: ROWS }, () =>
      Array.from({ length: COLS }, () => true)
  );
}
fillGrid();


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
    const pos:Vector2 = cell.pos
    ctx.fillRect(pos.x * CELL_SIZE, pos.y * CELL_SIZE, CELL_SIZE, CELL_SIZE);
  }
}


function update(){
  for (const cell of filled.value) {
    const pos:Vector2 = cell.pos

  }
}

function setCell(pos:Vector2, type:string) {
  filled.value.push({ pos: pos});
  grid?.value?[pos.x]?[pos.y] = true;
}

onMounted(() => {
  filled.value.push({ pos: { x: 0, y: 0 }});
  filled.value.push({ pos: { x: COLS - 1, y: 0 } });
  filled.value.push({ pos: { x: 0, y: ROWS - 1 } });
  filled.value.push({ pos: { x: COLS - 1, y: ROWS - 1 } });


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
