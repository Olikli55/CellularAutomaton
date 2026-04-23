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
  console.log(gridBuffer.value);
  for (let y = 0; y < ROWS; y++) {
    for (let x = 0; x < COLS; x++) {

      if (chekCell(x,y)) { // sand
        if (!chekCell(x,y+1)) { // air
          moveCell(x,y,x,y+1)
        }else  {
          const dirX = Math.random() >= 0.5 ? 1 : -1;
          if (!chekCell(x + dirX, y + 1)) {
            moveCell(x, y, x + dirX, y + 1)
          } else if (!chekCell(x + dirX * -1, y + 1)) {
            moveCell(x, y, x + dirX * -1, y + 1)
          }
        }
      }
    }
  }


}

function onCanvasDown(e: MouseEvent) {
  const el = canvas.value as HTMLCanvasElement | null;
  if (!el) return;

  const r = el.getBoundingClientRect();         // canvas position on screen
  const px = e.clientX - r.left;                // mouse X in canvas pixels
  const py = e.clientY - r.top;                 // mouse Y in canvas pixels

  const x = Math.floor(px / CELL_SIZE);         // cell X
  const y = Math.floor(py / CELL_SIZE);         // cell Y

  if (x < 0 || x >= COLS || y < 0 || y >= ROWS) return;

  gridBuffer.value[x]![y] = true;
  draw();
}

function moveCell(x:number, y:number, x1:number, y1:number) {
  if (x1 < 0 || x1 >= COLS || y1 < 0 || y1 >= ROWS) {
    return;
  }

  gridBuffer.value[x]![y] = false;
  gridBuffer.value[x1]![y1] = true;

}


function chekCell(x:number, y:number):boolean{
  if (x < 0 || x >=  COLS || y < 0|| y >= ROWS) {return true;} //wall


  if(grid.value[x]![y] != undefined){
    return grid.value[x]![y];
  }
  throw new Error("undefined grid")
}


onMounted(() => {
  if (!canvas.value) throw new Error("canvas not mounted");
  const c = canvas.value.getContext("2d");
  if (!c) throw new Error("no canvas context");
  ctx.value = c;
  fillGrid();
  //gridBuffer.value[0]![0] = true ; //no idea what is that !
  draw();
  //animate();

});

function animate() {
  clear();
  update();
  draw();
  newCell();
  requestAnimationFrame(animate); //  next frame
}

function newCell(){
  gridBuffer.value[50]![0] = true;
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
        @mousedown="onCanvasDown"

    />

  </div>
  <button @mousedown="animate()">next frame</button>
  <button @mousedown="newCell()">newcell</button>
  <button @mousedown=" draw()">draw</button>
  <button @mousedown="clear()"> clear</button>
</template>


<style scoped>


</style>