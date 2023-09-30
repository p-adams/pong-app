<script lang="ts">
  import { afterUpdate, onMount } from "svelte";
  const [width, height] = [600, 350];
  const [paddleWidth, paddleHeight] = [25, 75];
  const margin = 20;
  const leftPaddleSpeed = 6;
  const rightPaddleSpeed = 50;
  const ballSpeed = 4;
  const SCORE = 3;
  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;
  let leftScore = 0;
  let rightScore = 0;
  let leftPaddleDirection = 1; // 1 for moving down, -1 for moving up
  let ballDirectionX = 1; // 1 for moving right, -1 for moving left
  let ballDirectionY = 1; // 1 for moving down, -1 for moving up
  $: leftPaddleX = margin;
  $: leftPaddleY = (height - paddleHeight) / 2;
  $: rightPaddleX = width - paddleWidth - margin;
  $: rightPaddleY = (height - paddleHeight) / 2;
  $: bottom = height - paddleHeight - margin;
  $: ballX = height / 2;
  $: ballY = height / 2;
  $: hasWinner = leftScore === SCORE || rightScore === SCORE;

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
    x: ballX,
    y: ballY,
    draw: () => {
      ctx.beginPath();
      ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
      ctx.fillStyle = "white";
      ctx.fill();
      ctx.closePath();
      ballMove();
    },
  };

  function renderGame() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    net.draw();
    leftPaddle.draw();
    rightPaddle.draw();
    ball.draw();
    if (!hasWinner) {
      requestAnimationFrame(renderGame);
    }
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

  function ballMove() {
    // Update the ball's position based on direction
    ballX += ballSpeed * ballDirectionX;
    ballY += ballSpeed * ballDirectionY;

    // Check if the ball hits the right boundary
    if (ballX + ball.radius >= width - margin) {
      ballDirectionX = -1; // Reverse the X direction
    }

    // Check if the ball hits the left boundary
    if (ballX - ball.radius <= margin) {
      ballDirectionX = 1; // Reverse the X direction
    }

    // Check if the ball hits the top or bottom boundary
    if (
      ballY - ball.radius <= margin ||
      ballY + ball.radius >= height - margin
    ) {
      ballDirectionY *= -1; // Reverse the Y direction
    }

    // Check for collision with left paddle
    if (
      ballX - ball.radius <= leftPaddle.x + paddleWidth &&
      ballY >= leftPaddle.y &&
      ballY <= leftPaddle.y + paddleHeight
    ) {
      ballDirectionX = 1; // Reverse the X direction
    }

    // Check for collision with right paddle
    if (
      ballX + ball.radius >= rightPaddle.x &&
      ballY >= rightPaddle.y &&
      ballY <= rightPaddle.y + paddleHeight
    ) {
      ballDirectionX = -1; // Reverse the X direction
    }
    // Check if the ball goes past the left paddle (right player scores)
    if (ballX - ball.radius <= margin) {
      rightScore++;
      // Reset the ball's position to the center
      ballX = width / 2;
      ballY = height / 2;
      ballDirectionX = 1; // Start the ball moving to the right
      ballDirectionY = 1; // Start the ball moving down
    }

    // Check if the ball goes past the right paddle (left player scores)
    if (ballX + ball.radius >= width - margin) {
      leftScore++;
      // Reset the ball's position to the center
      ballX = width / 2;
      ballY = height / 2;
      ballDirectionX = -1; // Start the ball moving to the left
      ballDirectionY = 1; // Start the ball moving down
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
  <div class={`left-score ${leftScore === SCORE ? "win" : ""}`}>
    {leftScore}
  </div>
  <div class={`right-score ${rightScore === SCORE ? "win" : ""}`}>
    {rightScore}
  </div>
</div>
<canvas
  tabindex="0"
  {width}
  {height}
  bind:this={canvas}
  on:keydown={(e) => handlePaddleMove(e)}
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
  .left-score.win::after {
    content: "Left Player Wins!";
    margin-left: 10px;
  }

  .right-score.win::after {
    content: "Right Player Wins!";
    margin-left: 10px;
  }
</style>
