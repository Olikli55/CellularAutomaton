<script setup lang="ts">
import {onMounted, ref, shallowRef} from "vue";
const canvas = ref(null);
const CELL_SIZE = 10;
const COLS = 100;
const ROWS = 80;
const CANVAS_SIZE_H = CELL_SIZE * ROWS;
const CANVAS_SIZE_W = CELL_SIZE * COLS;
const ctx = shallowRef<CanvasRenderingContext2D | null>(null); //handling undefined, nfi what is  shallowRef
let timer:number;

//=========================*********
const grid = ref<number[][]>([]);
const gridBuffer = ref<number[][]>([]);
//===========================*********


function fillGrid() {
  grid.value = Array.from({ length: COLS }, () =>
      Array.from({ length: ROWS }, () => 0)
  );
  gridBuffer.value = Array.from({ length: COLS }, () =>
      Array.from({ length: ROWS }, () => 0)
  );
}


// function clear() {
//   if (!ctx.value) {return;}
//   ctx.value.clearRect(0, 0, ctx.value.canvas.width, ctx.value.canvas.height);
// }
//
// function draw() {
//   if (!ctx.value) {return;}
//
//   for (let y = 0; y < ROWS; y++) {
//     for (let x = 0; x < COLS; x++) {
//       switch (grid.value[x]![y]) {
//         case 0:
//           break;
//         case 1:
//           ctx.value.fillStyle = "#a88b21"
//           ctx.value.fillStyle = "#cfaf35"
//           ctx.value.fillRect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE);
//           break;
//
//
//       }
//     }
//   }
// }


function update(){
  grid.value = gridBuffer.value.map(col => col.slice()); // dear JavaScript go fuck yourself. i cant even use value = value without creating some unknown link that nobody told me about

  //console.log(JSON.parse(JSON.stringify(grid.value))); //normal console.log outputs a reference to a value that means it can change and mess up my debugging

  for (let y = 0; y < ROWS; y++) {
    for (let x = 0; x < COLS; x++) {

      switch (grid.value[x]![y]) {
        case 0: //air do nothing
          break;
        case 1: // sand
          updateSand(x,y); //i think this updates sand but i am not really sure
          break;
        case 2: //water
          updateWater(x,y);
      }


      }
    }
  }

function updateSand (x:number, y:number) {
  if (chekCell(x,y+1) == 0) {
    moveCell(x, y, x, y + 1);

  }else {
    const dirX = Math.random() >= 0.5 ? 1 : -1; //random direction left or right

    if (chekCell(x + dirX, y + 1) == 0) {
      moveCell(x, y, x + dirX, y + 1);

    } else if (chekCell(x + dirX * -1, y + 1) == 0) { //flip the dir
      moveCell(x, y, x + dirX * -1, y + 1);
    }
  }
}



function updateWater (x:number, y:number) {
  if (chekCell(x,y+1) == 0) {
    moveCell(x, y, x, y + 1); //down
    return;

  }
  let dirX = Math.random() >= 0.5 ? 1 : -1; //random direction left or right

  //===================== down + random side
  if (chekCell(x + dirX, y + 1) == 0) {moveCell(x, y, x + dirX, y + 1);return;}
  if (chekCell(x - dirX, y + 1) == 0) {moveCell(x, y, x - dirX, y + 1);return;}

  // ==================== same y level
  if (chekCell(x + dirX, y) === 0) { moveCell(x,y,x+dirX,y); return; }
  if (chekCell(x - dirX, y) === 0) { moveCell(x,y,x-dirX,y); return; }


}

function onCanvasDown(e: MouseEvent) {
  const el = canvas.value as HTMLCanvasElement | null;
  if (!el) return;

  const r = el.getBoundingClientRect();
  const px = e.clientX - r.left;
  const py = e.clientY - r.top;

  const x = Math.floor(px / CELL_SIZE);
  const y = Math.floor(py / CELL_SIZE);

  if (x < 0 || x >= COLS || y < 0 || y >= ROWS) return;

  for (let dy = -1; dy <= 2; dy++) {
    for (let dx = -1; dx <= 2; dx++) {
      const xx = x + dx;
      const yy = y + dy;
      if (xx < 0 || xx >= COLS || yy < 0 || yy >= ROWS) continue;
      gridBuffer.value[xx]![yy] = 0;
    }
  }
}

function moveCell(x:number, y:number, x1:number, y1:number) {
  if (!ctx.value) {return;}

  switch (grid.value[x]![y]) {
    case 1:
      if(Math.random() >= 0.3){
        ctx.value.fillStyle = "#cab15f"
      }else{
        ctx.value.fillStyle = "#bda659"}
      break;
    case 2:
      if(Math.random() >= 0.3){
        ctx.value.fillStyle = "#3861c1"
      }else{
        ctx.value.fillStyle = "#355cb8"}
      break;
  }

  gridBuffer.value[x1]![y1] = gridBuffer.value[x]![y]!;
  ctx.value.fillRect(x1 * CELL_SIZE, y1 * CELL_SIZE, CELL_SIZE, CELL_SIZE);


  gridBuffer.value[x]![y] = 0;
  ctx.value.fillStyle = "#ffffff"
  ctx.value.fillRect(x * CELL_SIZE, y * CELL_SIZE, CELL_SIZE, CELL_SIZE);


}


function chekCell(x:number, y:number):number{
  if (x < 0 || x >=  COLS || y < 0|| y >= ROWS) {return 1;} //wall

  return gridBuffer.value[x]![y]!;
}


onMounted(() => {
  if (!canvas.value) throw new Error("canvas not mounted");
  const c = canvas.value.getContext("2d");
  if (!c) throw new Error("no canvas context");
  ctx.value = c;
  fillGrid();
  //gridBuffer.value[0]![0] = true ; //no idea what is that !
  //animate();

});

function animate() {
 // clear();
  update();
  //draw();
  newCell();
  requestAnimationFrame(animate); //  next frame
}

function newCell(){
 // console.log(JSON.parse(JSON.stringify(grid.value)));
  gridBuffer.value[50]![0] = 2;
}

</script>





<template>
  <header>
    <button @mousedown="animate()">start</button>

  </header>
  <div style="display:grid">
    <canvas
        ref="canvas"
        :width="CANVAS_SIZE_W"
        :height="CANVAS_SIZE_H"
        @mousedown="onCanvasDown"

    />

  </div>

</template>


<style scoped>


</style>