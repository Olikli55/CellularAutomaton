
<script setup lang="ts">
import {onMounted, ref, shallowRef} from "vue";
const canvas = ref(null);
const CELL_SIZE = 10;
const COLS = 10;
const ROWS = 8;
const CANVAS_SIZE_H = CELL_SIZE * ROWS;
const CANVAS_SIZE_W = CELL_SIZE * COLS;
const ctx = shallowRef<CanvasRenderingContext2D | null>(null); //handling undefined, idk what is  shallowRef
let timer:number;

//=========================
const grid = ref<boolean[][]>([]);
const gridBuffer = ref<boolean[][]>([]);
//===========================


function fillGrid() {
  grid.value = Array.from({ length: ROWS }, () =>
      Array.from({ length: COLS }, () => false)
  );
  gridBuffer.value = Array.from({ length: ROWS }, () =>
      Array.from({ length: COLS }, () => false)
  );
}


function clear() {
  if (!ctx.value) {return;}
  ctx.value.clearRect(0, 0, ctx.value.canvas.width, ctx.value.canvas.height);
}

function draw() {
  if (!ctx.value) {return;}

  ctx.value.fillStyle = "#343"
  for (let y = 0; y < ROWS; y++) {
    for (let x = 0; x < COLS; x++) {
      if (! grid.value[y]![x]) {continue;}
      ctx.value.fillRect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE);
    }
  }
}


function update(){
  for (let y = 0; y < ROWS; y++) {
    for (let x = 0; x < COLS; x++) {
      if (grid.value[y]![x]) { // sand
        //console.log(grid.value[x]![y +1]);
        if (!grid.value[x]![y + 1]) { // air
          moveCell(x,y,x,y+1)
        }
      }
    }
  }
  grid.value = gridBuffer.value;

}

function moveCell(x:number, y:number, x1:number, y1:number) {
  if (x1 < 0 || x1 >= COLS || y1 < 0 || y1 >= ROWS) return;
  if (x < 0 || x >= COLS || y < 0 || y >= ROWS) return;

  gridBuffer.value[x]![y] = false;
  gridBuffer.value[x1]![y1] = true;

}

onMounted(() => {
  if (!canvas.value) throw new Error("canvas not mounted");
  const c = canvas.value.getContext("2d");
  if (!c) throw new Error("no canvas context");
  ctx.value = c;
  fillGrid();
  grid.value[0]![0] = true ; //no idea what is that !
  grid.value[0]![1] = true ; //no idea what is that !
  grid.value[0]![2] = true ; //no idea what is that !

  draw();

  timer = window.setInterval(() => {
    //clear();
    //update();
    draw();

  }, 1000 ); //
});

</script>





<template>
  <header>
  <h1>!!!!!!</h1>
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
