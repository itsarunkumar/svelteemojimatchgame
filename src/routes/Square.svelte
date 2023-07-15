<script lang="ts">
  import { get_twemoji_url } from "./utils";
  import { send } from "./transition";

  export let emoji: string;
  export let selected: boolean = false;
  export let found: boolean = false;
  export let group: "a" | "b";
</script>

<div class="square" class:flipped={selected || found}>
  <button on:click />

  <div class="background" />

  {#if !found}
    <img
      out:send={{ key: `${emoji}:${group}` }}
      src={get_twemoji_url(emoji)}
      alt={emoji}
    />
  {/if}
</div>

<style>
  .square {
    display: flex;
    width: 100%;
    height: 100%;
    align-items: center;
    justify-content: center;
    transition: filter 0.2s;
    transform-style: preserve-3d;
    transform: rotateY(180deg);
    transition: transform 0.4s;
    user-select: none;
  }

  .square * {
    backface-visibility: hidden;
  }

  button {
    position: absolute;
    width: 100%;
    height: 100%;
    border: none;
    display: flex;
    justify-content: center;
    align-items: center;
    background: var(--bg-2);
    border-radius: 1em;
    transform: rotateY(180deg);
    -webkit-tap-highlight-color: transparent;
  }

  button:disabled {
    color: inherit;
  }

  .flipped {
    transform: rotateY(0);
    z-index: 2;
  }

  .background {
    position: absolute;
    width: 100%;
    height: 100%;
    background: var(--bg-1);
    border: 2px solid var(--accent);
    border-radius: 1em;
    transition: border 0.2s;
    pointer-events: none;
  }

  .background.found {
    border: 2px solid var(--bg-2);
  }

  img {
    display: block;
    font-size: 6em;
    width: 1em;
    height: 1em;
    line-height: 1;
    z-index: 2;
    pointer-events: none;
  }
</style>
