<script>
  import { onMount } from 'svelte';
  import { Player } from 'tone'
  import Play from './Play.svelte'

  const BPM = 135
  const tick = BPM * 4

  let song, sample, started = false;
  let fwoots = {
    apple: { x: 0, y: 0, z: 0 }, 
    banana: { x: 0, y: 0, z: 0 }, 
    watermelon: { x: 0, y: 0, z: 0 }, 
    peach: { x: 0, y: 0, z: 0 }
  }

  onMount(() => {
    let interval;

    song = new Player('/sounds/wheres-my-fwoot.mp3').toDestination()
    song.loop = true
    sample = new Player('/sounds/fwoot-only.mp3').toDestination()

    interval = setInterval(() => {
      fwoots = Object.entries(fwoots).reduce((acc, [fwoot]) => {
        return {
          ...acc,
          [fwoot]: { x: genPozish(), y: genPozish(), z: genPozish() }
        }
      }, fwoots)
    }, tick)

    return () => {
      clearInterval(interval);
    }
  })

  const onPlay = () => { 
    started = true 
    song.start()
  }

  const onPlaySample = () => {
    sample?.start()
  }

  const genPozish = () => Math.random() * 400 - 200

</script>

<style>
  :global(body) {
    margin: 0;
    font-family: Arial, Helvetica, sans-serif;
    background-color: black;
  }
  main { 
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  button { 
    outline: none;
    border: none;
    background: none;
    padding: 20px 40px;
    cursor: pointer;
    width: 50%;
  }
  img { 
    max-width: 200px;
    transition: all var(--tick) ease;
    cursor: pointer;
    transform: translate3d(var(--x),var(--y),var(--z));
  }
  img:hover {
    transform: scale(1.2);
  }
</style>

<main>
  {#if !started}
    <button on:click={onPlay}>
      <Play />
    </button>
  {:else}
    {#each Object.entries(fwoots) as [fwoot, {x,y,z}]}
      <img 
        class={`fwoot ${fwoot}`} 
        on:click={onPlaySample} 
        alt={fwoot} 
        src={`/images/${fwoot}.png`}
        style={`
          --x: ${x}px;
          --y: ${y}px;
          --z: ${z}px;
          --tick: ${tick / 1000}s;
        `}
      />
    {/each}
  {/if}

</main>