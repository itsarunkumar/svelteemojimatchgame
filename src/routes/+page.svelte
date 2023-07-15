<script lang="ts">
  import Game from "./Game.svelte";
  import "../styles.css";
  import Modal from "./Modal.svelte";
  import { levels } from "./levels";
  import { confetti } from "@neoconfetti/svelte";

  let state: "waiting" | "playing" | "paused" | "won" | "lost" = "waiting";
  let game: Game;
</script>

<Game
  bind:this={game}
  on:play={() => {
    state = "playing";
  }}
  on:gameover={() => {
    state = "lost";
  }}
  on:win={() => {
    state = "won";
  }}
  on:pause={() => {
    state = "paused";
  }}
/>

{#if state != "playing"}
  <Modal>
    <header>
      <h1>e<span>match</span>i</h1>
      <p>the emoji matching game</p>
    </header>
    {#if state === "won" || state === "lost"}
      <p>you {state}! play again?</p>
    {:else if state === "paused"}
      <p>game paused</p>
    {:else if state === "waiting"}
      <p>choose a level:</p>
    {/if}

    <div class="buttons">
      {#if state === "paused"}
        <button on:click={() => game.resume()}>resume</button>
        <button on:click={() => (state = "waiting")}> quit </button>
      {:else}
        {#each levels as level}
          <button
            on:click={() => {
              game.start(level);
              state = "playing";
            }}
          >
            {level.label}
          </button>
        {/each}
      {/if}
    </div>
  </Modal>
{/if}

{#if state === "won"}
  <div
    class="confetti"
    use:confetti={{
      stageHeight: innerHeight,
      stageWidth: innerWidth,
    }}
  />
{/if}

<style>
  main {
    text-align: center;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  header {
    font-size: min(5vw, 2rem);
    font-family: Grandstander;
  }

  h1 {
    font-size: 4em;
    margin: 0;
    height: 1em;
  }

  h1 span {
    color: var(--accent);
  }

  p {
    margin: 0 0 1em 0;
  }

  .buttons {
    display: flex;
    justify-content: center;
    gap: 0.5em;
  }

  button {
    background: var(--accent);
    color: white;
    font-size: inherit;
    font-family: inherit;
    border: none;
    padding: 1em;
    border-radius: 0.5em;
  }

  .confetti {
    position: fixed;
    width: 100%;
    height: 100%;
    left: 50%;
    top: 30%;
    pointer-events: none;
  }
</style>
