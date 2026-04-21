<script setup lang="ts">
import {onMounted, ref, shallowRef} from "vue";
const canvas = ref(null);
const CELL_SIZE = 10;
const COLS = 100;
const ROWS = 80;
const CANVAS_SIZE_H = CELL_SIZE * ROWS;
const CANVAS_SIZE_W = CELL_SIZE * COLS;
const mousePos = ref();
const ctx = shallowRef<CanvasRenderingContext2D | null>(null); //handling undefined, idk what is  shallowRef
let timer:number;

//=========================
const grid = ref<boolean[][]>([]);
const gridBuffer = ref<boolean[][]>([]);
//===========================


function fillGrid() {
  grid.value = Array.from({ length: COLS }, () =>
      Array.from({ length: ROWS }, () => false)
  );
  gridBuffer.value = Array.from({ length: COLS }, () =>
      Array.from({ length: ROWS }, () => false)
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
      if (!grid.value[x]![y]) {continue;}
      ctx.value.fillRect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE);
    }
  }
}


function update(){
  grid.value = gridBuffer.value.map(col => col.slice()); // dear JavaScript go fuck yourself. i cant even use value = value without creating some unknown link that nobody told me about

  //console.log(JSON.parse(JSON.stringify(grid.value)));
  // i need to print it in current state thats why i need to use stringify. console.log() prints reference not an actual value

  for (let y = 0; y < ROWS; y++) {
    for (let x = 0; x < COLS; x++) {

      if (chekCell(x,y)) { // sand
        if (!chekCell(x,y+1)) { // air
          moveCell(x,y,x,y+1)
        }
        const dirX  = Math.random() >= 0.5 ? 1 : -1;
        if(!chekCell(x + dirX,y)) {
          moveCell(x,y,x+ dirX,y)
        }else if(chekCell(x+dirX*-1,y)) {
          moveCell(x,y,x+ dirX*-1,y)
        }
      }
    }
  }
  clear();
  draw();

}

function moveCell(x:number, y:number, x1:number, y1:number) {
  if (x1 < 0 || x1 >= COLS || y1 < 0 || y1 >= ROWS) {
    return;
  }
  if (x < 0 || x >= COLS || y < 0 || y >= ROWS) {
    return;
  }
  gridBuffer.value[x]![y] = false;
  gridBuffer.value[x1]![y1] = true;
}


function chekCell(x:number, y:number):boolean{
  if (x < 0 || x >=  COLS || y < 0|| y >= ROWS) {return true;} //wall


  if(gridBuffer.value[x]![y] != undefined){
    return gridBuffer.value[x]![y];
  }
  throw new Error("undefined grid")
}


onMounted(() => {
  if (!canvas.value) throw new Error("canvas not mounted");
  const c = canvas.value.getContext("2d");
  if (!c) throw new Error("no canvas context");
  ctx.value = c;
  fillGrid();
  gridBuffer.value[0]![0] = true ; //no idea what is that !

  draw();
  grid.value[0]![0] = true;

  animate();

});

function animate() {
  clear();
  update();
  draw();
  newCell();
  requestAnimationFrame(animate); // schedule next frame
}

function newCell(){
  gridBuffer.value[0]![0] = true;
}

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
  <button @mousedown="start()">start</button>
  <button @mousedown="newCell()">newcell</button>
</template>


<style scoped>


</style>