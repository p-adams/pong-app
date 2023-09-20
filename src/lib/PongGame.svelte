<script lang="ts">
  import { afterUpdate, onMount } from "svelte";
  const [width, height] = [600, 350];
  const [paddleWidth, paddleHeight] = [25, 75];

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;

  onMount(() => {
    ctx = canvas.getContext("2d")!;
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    leftPaddle.draw();
    rightPaddle.draw();
  });

  afterUpdate(() => {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    leftPaddle.draw();
    rightPaddle.draw();
  });

  $: leftPaddleX = 0;
  $: leftPaddleY = (height - paddleHeight) / 2;
  $: rightPaddleX = width - paddleWidth;
  $: rightPaddleY = (height - paddleHeight) / 2;

  $: leftPaddle = {
    x: leftPaddleX,
    y: leftPaddleY,
    draw: () => {
      ctx.save();
      ctx.beginPath();
      ctx.rect(leftPaddle.x, leftPaddle.y, paddleWidth, paddleHeight);
      ctx.fillStyle = "white";
      ctx.fill();
      ctx.closePath();
      ctx.restore();
    },
  };
  $: rightPaddle = {
    x: rightPaddleX,
    y: rightPaddleY,
    draw: () => {
      ctx.save();
      ctx.beginPath();
      ctx.rect(rightPaddle.x, leftPaddle.y, paddleWidth, paddleHeight);
      ctx.fillStyle = "white";
      ctx.fill();
      ctx.closePath();
      ctx.restore();
    },
  };
</script>

<canvas {width} {height} bind:this={canvas} />

<style>
  canvas {
    outline: 1px solid lightgray;
    background-color: #444;
  }
</style>
