<script lang="ts">
  import PongGame from "./lib/PongGame.svelte";
</script>

<main>
  <div>
    <h1>Pong</h1>

    <div class="card">
      <PongGame />
    </div>

    <p>
      How to Play <a
        href="https://github.com/sveltejs/kit#readme"
        target="_blank"
        rel="noreferrer">Guide</a
      >
    </p>
  </div>
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
  .read-the-docs {
    color: #888;
  }
</style>
