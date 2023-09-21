<script lang="ts">
  import { afterUpdate, onMount } from "svelte";
  const [width, height] = [600, 350];
  const [paddleWidth, paddleHeight] = [25, 75];
  const [netWidth, netHeight] = [25, 350];

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;

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

  $: net = {
    x: width / 2,
    y: height,
    draw: () => {
      ctx.setLineDash([10, 5]);
      ctx.strokeStyle = "white";
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.moveTo(net.x, 1);
      ctx.lineTo(net.x, net.y);
      ctx.stroke();
      ctx.closePath();
    },
  };

  function renderGame() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    net.draw();
    leftPaddle.draw();
    rightPaddle.draw();
  }

  onMount(() => {
    ctx = canvas.getContext("2d")!;
    renderGame();
  });

  afterUpdate(() => {
    renderGame();
  });
</script>

<canvas {width} {height} bind:this={canvas} />

<style>
  canvas {
    outline: 1px solid lightgray;
    background-color: #444;
  }
</style>
