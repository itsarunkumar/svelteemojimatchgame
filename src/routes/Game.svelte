<script lang="ts">
  import { createEventDispatcher, onMount } from "svelte";
  import Countdown from "./Countdown.svelte";
  import Found from "./Found.svelte";
  import Grid from "./Grid.svelte";
  import { levels } from "./levels";
  import type { Level } from "./levels";
  import { shuffle } from "./utils";

  const dispatch = createEventDispatcher();

  let size: number;
  let grid: string[] = [];
  let found: string[] = [];
  let remaining: number = 0;
  let duration: number = 0;
  let playing: boolean = false;

  export function start(level: Level) {
    size = level.size;
    remaining = duration = level.duration;

    grid = createGrid(level);

    // const sliced = level.emojis.slice();
    // const pairs: string[] = [];

    // pick a set of emojis at random
    // for (let i = 0; i < (size * size) / 2; i += 1) {
    // 	const index = Math.floor(Math.random() * sliced.length);
    // 	const emoji = sliced[index];
    // 	sliced.splice(index, 1);
    // 	pairs.push(emoji);
    // }

    // // repeat the set
    // grid = shuffle([...pairs, ...pairs]);
    // found = [];

    resume();
  }

  export function resume() {
    playing = true;
    countdown();

    dispatch("play");
  }

  function createGrid(level: Level) {
    const copy = level.emojis.slice();
    const pairs: string[] = [];
    for (let i = 0; i < level.size ** 2 / 2; i += 1) {
      const index = Math.floor(Math.random() * copy.length);
      const emoji = copy.splice(index, 1);
      pairs.push(emoji);
    }

    pairs.push(...pairs);

    return shuffle(pairs);
  }

  function countdown() {
    const start = Date.now();
    let remaining_at_start = remaining;

    function loop() {
      if (!playing) return;

      requestAnimationFrame(loop);

      remaining = remaining_at_start - (Date.now() - start);
      if (remaining <= 0) {
        //TODO game over
        dispatch("gameover");
        playing = false;
      }
    }
    loop();
  }
</script>

<div class="game" style="--size : {size}">
  <div class="info">
    {#if playing}
      <Countdown
        {remaining}
        {duration}
        on:click={() => {
          //TODO pause
          playing = false;
          dispatch("pause");
        }}
      />
    {/if}
  </div>

  <div class="grid">
    <Grid
      {grid}
      on:found={(e) => {
        found = [...found, e.detail.emoji];

        if (found.length === (size * size) / 2) {
          //TODO win
          dispatch("win");
        }
      }}
      {found}
    />
  </div>

  <div class="info">
    <!-- found emojis -->
    <Found {found} />
  </div>
</div>

<style>
  .game {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    font-size: min(1vmin, 0.5em);
    gap: 2em;
    height: 100%;
  }

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

  .info {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 80em;
    height: 10em;
  }

  @media (min-aspect-ratio: 1/1) {
    .game {
      flex-direction: row-reverse;
    }

    .info {
      width: 10em;
      height: 80em;
    }
  }
</style>
