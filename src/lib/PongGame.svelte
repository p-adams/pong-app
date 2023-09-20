<script lang="ts">
  import { onMount } from "svelte";
  const [width, height] = [600, 350];
  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;

  onMount(() => {
    ctx = canvas.getContext("2d")!;
    ctx.fillStyle = "green";
    ctx.fillRect(0, 0, width, height);
  });
</script>

<canvas {width} {height} bind:this={canvas} />

<style>
  canvas {
    outline: 1px solid lightgray;
  }
</style>
