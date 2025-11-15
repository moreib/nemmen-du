<script>
  import { tweened } from 'svelte/motion';
  import { cubicOut } from 'svelte/easing';

  // Base player rotation positions (R1 → R6)
  let rotationPositions = [
    { x: 20, y: 80 },
    { x: 50, y: 80 },
    { x: 80, y: 80 },
    { x: 20, y: 40 },
    { x: 50, y: 40 },
    { x: 80, y: 40 }
  ];

  // Strategy positions
  const attackPositions = [
    { x: 30, y: 75 },
    { x: 50, y: 82 },
    { x: 75, y: 75 },
    { x: 25, y: 35 },
    { x: 50, y: 25 },
    { x: 75, y: 35 }
  ];

  const defensePositions = [
    { x: 20, y: 85 },
    { x: 50, y: 90 },
    { x: 80, y: 85 },
    { x: 20, y: 55 },
    { x: 50, y: 50 },
    { x: 80, y: 55 }
  ];

  let mode = "attack";

  // Tweened player positions for animation
  let players = rotationPositions.map(p => ({
    pos: tweened(p, { duration: 500, easing: cubicOut })
  }));

  function applyPositions(target) {
    target.forEach((p, i) => {
      players[i].pos.set(p);
    });
  }

  // Apply strategy instantly on start
  applyPositions(attackPositions);

  function rotate() {
    rotationPositions.unshift(rotationPositions.pop());
    applyPositions(rotationPositions);
  }

  function toggleMode() {
    mode = mode === "attack" ? "defense" : "attack";
    applyPositions(mode === "attack" ? attackPositions : defensePositions);
  }
</script>

<style>
  .court {
    width: 600px;
    height: 300px;
    background: #a6d47a;
    border: 4px solid #333;
    position: relative;
    margin: 20px auto;
  }

  .net {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 6px;
    background: #444;
  }

  .player {
    position: absolute;
    width: 26px;
    height: 26px;
    background: #ff5c5c;
    border-radius: 50%;
    border: 2px solid #222;
    transform: translate(-50%, -50%);
  }

  .ui {
    display: flex;
    gap: 20px;
    justify-content: center;
    margin-bottom: 10px;
  }

  button, label {
    font-size: 18px;
    cursor: pointer;
  }
</style>

<h1 style="text-align:center;">Volleyball Strategy Visualizer</h1>

<div class="ui">
  <button on:click={rotate}>Rotate</button>

  <label>
    <input type="checkbox" on:change={toggleMode} checked={mode === "defense"} />
    {mode.toUpperCase()}
  </label>

  <strong>Strategy: {mode === 'attack' ? 'Attack Formation' : 'Defense Formation'}</strong>
</div>

<div class="court">
  <div class="net"></div>

  {#each players as p}
    <!-- Subscribe to tweened store -->
    {#await p.pos}
      <!-- nothing -->
    {:then value}
      <div class="player"
        style="left:{value.x}%; top:{value.y}%;">
      </div>
    {/await}
  {/each}
</div>
