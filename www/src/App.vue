<script setup lang="ts">
import { Universe, Cell } from "game-of-life-rs";
import { memory } from "game-of-life-rs/game_of_life_rs_bg.wasm";
import { ref, onMounted, onUnmounted } from "vue";

const CELL_SIZE = 5; // px
const GRID_COLOR = "#CCCCCC";
const DEAD_COLOR = "#FFFFFF";
const ALIVE_COLOR = "#000000";

const universe = Universe.new();
const width = universe.width();
const height = universe.height();

const canvas = ref<HTMLCanvasElement | null>(null);

const getIndex = (row: number, col: number) => row * width + col;

type Effect = {
  (): void;
};
const unmountedEffects: Effect[] = [];

function drawCells(ctx: CanvasRenderingContext2D) {
  const cellsPtr = universe.cells();
  const cells = new Uint8Array(memory.buffer, cellsPtr, width * height);

  ctx.beginPath();

  for (let row = 0; row < height; row++) {
    for (let col = 0; col < width; col++) {
      const idx = getIndex(row, col);

      ctx.fillStyle = cells[idx] === Cell.Dead ? DEAD_COLOR : ALIVE_COLOR;

      ctx.fillRect(
        col * (CELL_SIZE + 1) + 1,
        row * (CELL_SIZE + 1) + 1,
        CELL_SIZE,
        CELL_SIZE
      );
    }
  }
  ctx.stroke();
}

function drawGrid(ctx: CanvasRenderingContext2D) {
  ctx.beginPath();
  ctx.strokeStyle = GRID_COLOR;

  for (let i = 0; i <= width; i++) {
    ctx.moveTo(i * (CELL_SIZE + 1) + 1, 0);
    ctx.lineTo(i * (CELL_SIZE + 1) + 1, (CELL_SIZE + 1) * height + 1);
  }

  for (let j = 0; j <= height; j++) {
    ctx.moveTo(0, j * (CELL_SIZE + 1) + 1);
    ctx.lineTo((CELL_SIZE + 1) * width + 1, j * (CELL_SIZE + 1) + 1);
  }

  ctx.stroke();
}

function tick(ctx: CanvasRenderingContext2D) {
  universe.tick();
  drawGrid(ctx);
  drawCells(ctx);
}

onMounted(() => {
  if (canvas.value === null) throw new Error("canvas not found.");

  canvas.value.height = (CELL_SIZE + 1) * height + 1;
  canvas.value.width = (CELL_SIZE + 1) * width + 1;

  const CONTEXT: "2d" = "2d";
  const ctx = canvas.value.getContext(CONTEXT);
  if (ctx === null) throw new Error(`failed to get canvas context: ${CONTEXT}`);

  drawGrid(ctx);
  drawCells(ctx);

  const interval = setInterval(tick, 750, ctx);
  unmountedEffects.push(() => clearInterval(interval));
});

onUnmounted(() => {
  unmountedEffects.forEach((fx) => fx());
});
</script>

<template>
  <div id="game-of-life">
    <canvas ref="canvas"></canvas>
  </div>
</template>

<style scoped>
#game-of-life {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  gap: 4rem;
}

pre {
  font-size: 8px;
}
</style>
