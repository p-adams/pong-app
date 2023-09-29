<script lang="ts">
  import { afterUpdate, onMount } from "svelte";
  const [width, height] = [600, 350];
  const [paddleWidth, paddleHeight] = [25, 75];
  const margin = 20;
  const leftPaddleSpeed = 6;
  const rightPaddleSpeed = 50;
  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;
  let leftScore = 0;
  let rightScore = 0;
  let leftPaddleDirection = 1; // 1 for moving down, -1 for moving up

  $: leftPaddleX = margin;
  $: leftPaddleY = (height - paddleHeight) / 2;
  $: rightPaddleX = width - paddleWidth - margin;
  $: rightPaddleY = (height - paddleHeight) / 2;
  $: bottom = height - paddleHeight - margin;

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
      paddleLeftMove();
    },
  };
  $: rightPaddle = {
    x: rightPaddleX,
    y: rightPaddleY,
    draw: () => {
      ctx.save();
      ctx.beginPath();
      ctx.rect(rightPaddle.x, rightPaddle.y, paddleWidth, paddleHeight);
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

  $: ball = {
    radius: 10,
    draw: () => {
      ctx.beginPath();
      ctx.arc(10, 10, ball.radius, 0, Math.PI * 2);
      ctx.fillStyle = "white";
      ctx.fill();
      ctx.closePath();
    },
  };

  function renderGame() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    net.draw();
    leftPaddle.draw();
    rightPaddle.draw();
    ball.draw();
    requestAnimationFrame(renderGame);
  }

  function handlePaddleMove(
    e: KeyboardEvent & {
      currentTarget: EventTarget & HTMLCanvasElement;
    }
  ) {
    if (e.key === "ArrowUp" && rightPaddleY > margin) {
      rightPaddleY -= rightPaddleSpeed;
    } else if (
      e.key === "ArrowDown" &&
      rightPaddleY < height - paddleHeight - margin
    ) {
      rightPaddleY += rightPaddleSpeed;
    }
  }

  function paddleLeftMove() {
    const leftPaddleTop = leftPaddleY + margin;
    const leftPaddleBottom = leftPaddleY - margin;
    // Reverse direction if the paddle reaches the top or bottom
    if (leftPaddleTop <= margin || leftPaddleBottom >= bottom) {
      leftPaddleDirection *= -1;
    }
    // Update leftPaddleY based on direction
    leftPaddleY += leftPaddleSpeed * leftPaddleDirection;
  }

  onMount(() => {
    ctx = canvas.getContext("2d")!;
    canvas.focus();
    renderGame();
  });
</script>

<div class="score--outer">
  <div id="left-score">{leftScore}</div>
  <div id="right-score">{rightScore}</div>
</div>
<canvas
  tabindex="0"
  {width}
  {height}
  bind:this={canvas}
  on:keyup={(e) => handlePaddleMove(e)}
/>

<style>
  .score--outer {
    display: grid;
    grid-template-columns: 1fr 1fr;
    background-color: #444;
  }
  .score--outer div {
    font-size: x-large;
    color: white;
  }
  canvas {
    outline: 1px solid lightgray;
    background-color: #444;
  }
</style>
