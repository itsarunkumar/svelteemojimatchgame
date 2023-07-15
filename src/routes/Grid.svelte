<script lang="ts">
  import { createEventDispatcher } from "svelte";
  import Square from "./Square.svelte";
  export let grid: string[];
  export let found: string[];

  let a = -1;
  let b = -1;

  const dispatch = createEventDispatcher();

  let reset_timeout: number;
</script>

<div class="grid">
  {#each grid as emoji, i}
    <Square
      {emoji}
      on:click={() => {
        clearTimeout(reset_timeout);

        if (a === -1 && b === -1) {
          a = i;
        } else if (b === -1) {
          b = i;
          if (grid[a] === grid[b]) {
            //correct
            found = [...found, grid[a]];
            dispatch("found", {
              emoji,
            });
          } else {
            //incorrect
            reset_timeout = setTimeout(() => {
              a = -1;
              b = -1;
            }, 1000);
          }
        } else {
          a = i;
          b = -1;
        }
      }}
      selected={a === i || b === i}
      found={found.includes(emoji)}
      group={grid.indexOf(emoji) === i ? "a" : "b"}
    />
  {/each}
</div>

<style>
  .grid {
    display: grid;
    grid-template-columns: repeat(var(--size), 1fr);
    grid-template-rows: repeat(var(--size), 1fr);
    grid-gap: 1em;
    width: 80em;
    aspect-ratio: 1;
    perspective: 100vw;
    z-index: 2;
  }
</style>
