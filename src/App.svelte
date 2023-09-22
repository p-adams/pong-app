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
  .instructions {
    font-weight: bolder;
  }
</style>
