<script>
  import { onMount } from 'svelte';

  // Define all 6 rotations with their base, attack, and defense positions
  // Each player has a role: A (Attacker), M (Middle), Z (Setter/Zuspieler)
  const playerRoles = ['A', 'M', 'Z', 'A', 'M', 'Z']; // Roles for players 1-6

  // Player names (editable) - load from localStorage
  let playerNames = ['', '', '', '', '', ''];
  let currentRotation = 0;
  let mode = 'base';
  let sidebarOpen = false;

  // Load saved state on mount
  onMount(() => {
    if (typeof window !== 'undefined' && typeof localStorage !== 'undefined') {
      try {
        const saved = localStorage.getItem('volleyballState');
        if (saved) {
          const state = JSON.parse(saved);
          if (state.playerNames) playerNames = state.playerNames;
          if (state.currentRotation !== undefined) currentRotation = state.currentRotation;
          if (state.mode) mode = state.mode;
        }
      } catch (e) {
        console.error('Error loading state:', e);
      }
    }
  });

  // Save state to localStorage
  function saveState() {
    if (typeof window !== 'undefined' && typeof localStorage !== 'undefined') {
      try {
        const state = {
          playerNames,
          currentRotation,
          mode
        };
        localStorage.setItem('volleyballState', JSON.stringify(state));
      } catch (e) {
        console.error('Error saving state:', e);
      }
    }
  }

  // Watch for changes and save
  $: playerNames, saveState();
  $: currentRotation, saveState();
  $: mode, saveState();

  // Role colors
  const roleColors = {
    'A': '#ef4444', // Red for Attackers
    'M': '#3b82f6', // Blue for Middles
    'Z': '#22c55e'  // Green for Setters
  };


  const base_positions = {
    5: { x: 20, y: 60 },
    6: { x: 50, y: 60 },
    1: { x: 80, y: 60 },
    2: { x: 80, y: 10 },
    3: { x: 50, y: 10 },
    4: { x: 20, y: 10 }
  }

  const attack_positions = {
    5: { x: 30, y: 50 },
    6: { x: 50, y: 80 },
    1: { x: 70, y: 50 },
    2: { x: 80, y: 10 },
    3: { x: 50, y: 10 },
    4: { x: 20, y: 10 }
  }


  const rotations = [
    {
      id: 1,
      base: [
        base_positions[5],
        base_positions[6],
        base_positions[1],
        base_positions[2],
        base_positions[3],
        base_positions[4],
      ],
      attack: [
        attack_positions[5],
        attack_positions[6],
        attack_positions[1],
        attack_positions[4],
        attack_positions[3],
        attack_positions[2],
      ],
      defense: [
        // Laufer 1
        {x: base_positions[5].x + 30, y: base_positions[5].y + 5},
        {x: base_positions[6].x + 30, y: base_positions[6].y + 5},
        {x: base_positions[1].x, y: base_positions[1].y - 45},
        {x: base_positions[2].x + 10, y: base_positions[2].y - 5},
        base_positions[3],
        {x: base_positions[4].x, y: base_positions[4].y + 45},

      ]
    },
    {
      id: 2,
      base: [
        base_positions[4],
        base_positions[5],
        base_positions[6],
        base_positions[1],
        base_positions[2],
        base_positions[3],
      ],
      attack: [
        attack_positions[4],
        attack_positions[6],
        attack_positions[1],
        attack_positions[5],
        attack_positions[3],
        attack_positions[2],
      ],
      defense: [
        // Laufer 6
        base_positions[4],
        base_positions[5],
        {x: base_positions[6].x + 25, y: base_positions[6].y-45},
        base_positions[1],
        {x: base_positions[2].x - 30, y: base_positions[2].y + 45},
        {x: base_positions[3].x + 10, y: base_positions[3].y},
      ]
    },
    {
      id: 3,
      base: [
        base_positions[3],
        base_positions[4],
        base_positions[5],
        base_positions[6],
        base_positions[1],
        base_positions[2],
      ],
      attack: [
        attack_positions[4],
        attack_positions[3],
        attack_positions[1],
        attack_positions[5],
        attack_positions[6],
        attack_positions[2],
      ],
      defense: [
        // Laufer 5
        {x: base_positions[3].x - 25, y: base_positions[3].y + 45},
        base_positions[4],
        {x: base_positions[5].x + 25, y: base_positions[5].y - 40},
        base_positions[6],
        base_positions[1],
        base_positions[2],
      ]
    },
    {
      id: 4,
      base: [
        base_positions[2],
        base_positions[3],
        base_positions[4],
        base_positions[5],
        base_positions[6],
        base_positions[1],
      ],
      attack: [
        attack_positions[4],
        attack_positions[3],
        attack_positions[2],
        attack_positions[5],
        attack_positions[6],
        attack_positions[1],
      ],
      defense: [
        {x: base_positions[2].x + 10, y: base_positions[2].y - 5},
        base_positions[3],
        {x: base_positions[4].x, y: base_positions[4].y + 45},
        {x: base_positions[5].x + 30, y: base_positions[5].y + 5},
        {x: base_positions[6].x + 30, y: base_positions[6].y + 5},
        {x: base_positions[1].x, y: base_positions[1].y - 45},
      ]
    },
     {
      id: 5,
      base: [
        base_positions[1],
        base_positions[2],
        base_positions[3],
        base_positions[4],
        base_positions[5],
        base_positions[6],
      ],
      attack: [
        attack_positions[5],
        attack_positions[3],
        attack_positions[2],
        attack_positions[4],
        attack_positions[6],
        attack_positions[1],
      ],
      defense: [
        base_positions[1],
        {x: base_positions[2].x - 30, y: base_positions[2].y + 45},
        {x: base_positions[3].x + 10, y: base_positions[3].y},
        base_positions[4],
        base_positions[5],
        {x: base_positions[6].x + 25, y: base_positions[6].y-45},
      ]
    },
    {
      id: 6,
      base: [
        base_positions[6],
        base_positions[1],
        base_positions[2],
        base_positions[3],
        base_positions[4],
        base_positions[5],
      ],
      attack: [
        attack_positions[5],
        attack_positions[6],
        attack_positions[2],
        attack_positions[4],
        attack_positions[3],
        attack_positions[1],
      ],
      defense: [
        base_positions[6],
        base_positions[1],
        base_positions[2],
        {x: base_positions[3].x - 25, y: base_positions[3].y + 45},
        base_positions[4],
        {x: base_positions[5].x + 25, y: base_positions[5].y - 40},
      ]
    },
  ];

  let currentPositions = rotations[0].base.map(pos => ({ ...pos }));

  // Smoothly transition positions when rotation or mode changes
  $: {
    const targetPositions = rotations[currentRotation][mode];
    currentPositions = targetPositions.map(pos => ({ ...pos }));
  }

  function rotate() {
    currentRotation = (currentRotation + 1) % 6;
  }

  function toggleSidebar() {
    sidebarOpen = !sidebarOpen;
  }
</script>

<style>
  * {
    box-sizing: border-box;
  }

  .container {
    min-height: 100vh;
    background: #f3f4f6;
    padding: 1rem;
  }

  h1 {
    text-align: center;
    font-size: 1.5rem;
    font-weight: bold;
    margin-bottom: 1rem;
  }

  @media (min-width: 768px) {
    h1 {
      font-size: 2rem;
      margin-bottom: 1.5rem;
    }
  }

  .main-wrapper {
    display: flex;
    max-width: 1400px;
    margin: 0 auto;
    position: relative;
  }

  /* Sidebar styles */
  .sidebar {
    position: fixed;
    top: 0;
    left: 0;
    height: 100vh;
    width: 280px;
    background: white;
    border-right: 1px solid #e5e7eb;
    transform: translateX(-100%);
    transition: transform 0.3s ease;
    z-index: 1000;
    overflow-y: auto;
  }

  .sidebar.open {
    transform: translateX(0);
  }

  @media (min-width: 1024px) {
    .sidebar {
      position: relative;
      transform: translateX(0);
      flex-shrink: 0;
    }
  }

  .sidebar-header {
    padding: 1rem;
    border-bottom: 1px solid #e5e7eb;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .sidebar-title {
    font-size: 1.125rem;
    font-weight: 600;
    color: #111827;
  }

  .close-btn {
    background: none;
    border: none;
    color: #6b7280;
    cursor: pointer;
    padding: 0.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 0.375rem;
  }

  .close-btn:hover {
    background: #f3f4f6;
    color: #111827;
  }

  @media (min-width: 1024px) {
    .close-btn {
      display: none;
    }
  }

  .sidebar-content {
    padding: 1rem;
  }

  .player-input-row {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    margin-bottom: 0.75rem;
    padding: 0.5rem;
    border-radius: 0.5rem;
    transition: background-color 0.15s;
  }

  .player-input-row:hover {
    background-color: #f9fafb;
  }

  .player-label {
    font-weight: 600;
    min-width: 60px;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.875rem;
  }

  .role-badge {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 24px;
    height: 24px;
    border-radius: 50%;
    font-size: 0.75rem;
    color: white;
    font-weight: bold;
  }

  .player-input-row input {
    flex: 1;
    padding: 0.5rem 0.75rem;
    border: 1px solid #d1d5db;
    border-radius: 0.5rem;
    font-size: 0.875rem;
  }

  .player-input-row input:focus {
    outline: none;
    border-color: #3b82f6;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
  }

  /* Overlay for mobile */
  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.5);
    z-index: 999;
    border: none;
    padding: 0;
    cursor: pointer;
    width: 100%;
    height: 100%;
  }

  @media (min-width: 1024px) {
    .overlay {
      display: none !important;
    }
  }

  /* Toggle button */
  .menu-btn {
    position: fixed;
    top: 1rem;
    left: 1rem;
    z-index: 998;
    background: white;
    border: 1px solid #e5e7eb;
    border-radius: 0.5rem;
    padding: 0.5rem;
    cursor: pointer;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  }

  .menu-btn:hover {
    background: #f9fafb;
  }

  @media (min-width: 1024px) {
    .menu-btn {
      display: none;
    }
  }

  /* Main content */
  .main-content {
    flex: 1;
    padding: 0;
  }

  @media (min-width: 1024px) {
    .main-content {
      padding-left: 2rem;
    }
  }

  .controls {
    display: flex;
    gap: 0.75rem;
    justify-content: center;
    margin-bottom: 1rem;
    align-items: center;
    flex-wrap: wrap;
    padding: 0 0.5rem;
  }

  button:not(.close-btn):not(.menu-btn) {
    padding: 0.5rem 1.25rem;
    background: #3b82f6;
    color: white;
    border: none;
    border-radius: 0.375rem;
    cursor: pointer;
    font-size: 0.95rem;
    white-space: nowrap;
  }

  button:not(.close-btn):not(.menu-btn):hover {
    background: #2563eb;
  }

  .control-group {
    display: flex;
    align-items: center;
    gap: 0.5rem;
  }

  label {
    font-weight: 600;
    font-size: 0.9rem;
  }

  select {
    padding: 0.4rem 0.75rem;
    border: 1px solid #d1d5db;
    border-radius: 0.375rem;
    font-size: 0.9rem;
  }

  .court {
    position: relative;
    width: 100%;
    max-width: 600px;
    aspect-ratio: 3 / 2;
    background: #f5deb3;
    border: 4px solid #1f2937;
    margin: 0 auto;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(2, 1fr);
  }

  .net {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 8px;
    background: #1f2937;
    z-index: 1;
  }

  .zone {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 2rem;
    font-weight: bold;
    color: rgba(0, 0, 0, 0.15);
    user-select: none;
  }

  @media (min-width: 640px) {
    .zone {
      font-size: 3rem;
    }
  }

  .zone:nth-child(2),
  .zone:nth-child(4),
  .zone:nth-child(6) {
    background: rgba(139, 69, 19, 0.08);
  }

  .zone:nth-child(3),
  .zone:nth-child(5),
  .zone:nth-child(7) {
    background: rgba(139, 69, 19, 0.04);
  }

  .player {
    position: absolute;
    width: 28px;
    height: 28px;
    border-radius: 50%;
    border: 2px solid #1f2937;
    transform: translate(-50%, -50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size: 0.6rem;
    font-weight: bold;
    color: white;
    z-index: 10;
    line-height: 1;
  }

  @media (min-width: 640px) {
    .player {
      width: 34px;
      height: 34px;
      font-size: 0.65rem;
    }
  }

  .player-name {
    position: absolute;
    top: -20px;
    left: 50%;
    transform: translateX(-50%);
    background: rgba(0, 0, 0, 0.8);
    color: white;
    padding: 2px 4px;
    border-radius: 3px;
    font-size: 0.6rem;
    white-space: nowrap;
    pointer-events: none;
    max-width: 80px;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  @media (min-width: 640px) {
    .player-name {
      top: -24px;
      padding: 2px 6px;
      font-size: 0.7rem;
      max-width: 120px;
    }
  }

  .player-number {
    font-size: 0.65rem;
  }

  @media (min-width: 640px) {
    .player-number {
      font-size: 0.7rem;
    }
  }

  .player-role {
    font-size: 0.55rem;
    opacity: 0.9;
  }

  @media (min-width: 640px) {
    .player-role {
      font-size: 0.6rem;
    }
  }

  .status {
    text-align: center;
    margin-top: 1rem;
    color: #4b5563;
  }

  .status p {
    font-weight: 600;
  }
</style>

<div class="container">
  <h1>Volleyball Strategy Visualizer</h1>

  <!-- Mobile menu button -->
  <button class="menu-btn" on:click={toggleSidebar} aria-label="Toggle menu">
    <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 20 20">
      <path fill-rule="evenodd" d="M3 5a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zM3 15a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1z" clip-rule="evenodd"></path>
    </svg>
  </button>

  <!-- Overlay -->
  {#if sidebarOpen}
    <button class="overlay visible" on:click={toggleSidebar} aria-label="Close sidebar"></button>
  {/if}

  <div class="main-wrapper">
    <!-- Sidebar -->
    <aside class="sidebar" class:open={sidebarOpen}>
      <div class="sidebar-header">
        <h2 class="sidebar-title">Player Names</h2>
        <button class="close-btn" on:click={toggleSidebar} aria-label="Close sidebar">
          <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
            <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd"></path>
          </svg>
        </button>
      </div>
      <div class="sidebar-content">
        {#each playerNames as name, idx}
          <div class="player-input-row">
            <span class="player-label">
              <span class="role-badge" style="background-color: {roleColors[playerRoles[idx]]}">
                {playerRoles[idx]}
              </span>
              P{idx + 1}
            </span>
            <input
              type="text"
              bind:value={playerNames[idx]}
              placeholder="Enter name"
            />
          </div>
        {/each}
      </div>
    </aside>

    <!-- Main content -->
    <main class="main-content">
      <div class="controls">
        <button on:click={rotate}>Rotate</button>

        <div class="control-group">
          <label for="formation">Formation:</label>
          <select id="formation" bind:value={mode}>
            <option value="base">Base</option>
            <option value="attack">Attack</option>
            <option value="defense">Defense</option>
          </select>
        </div>

        <div class="control-group">
          <label for="rotation">Rotation:</label>
          <select id="rotation" bind:value={currentRotation}>
            {#each rotations as r, idx}
              <option value={idx}>R{r.id}</option>
            {/each}
          </select>
        </div>
      </div>

      <div class="court">
        <div class="net"></div>
        <div class="zone">4</div>
        <div class="zone">3</div>
        <div class="zone">2</div>
        <div class="zone">5</div>
        <div class="zone">6</div>
        <div class="zone">1</div>
        {#each currentPositions as pos, idx (idx)}
          <div
            class="player"
            style="left: {pos.x}%; top: {pos.y}%; background-color: {roleColors[playerRoles[idx]]}; transition: all 0.5s cubic-bezier(0.33, 1, 0.68, 1);"
          >
            {#if playerNames[idx]}
              <span class="player-name">{playerNames[idx]}</span>
            {/if}
            <span class="player-number">{idx + 1}</span>
            <span class="player-role">{playerRoles[idx]}</span>
          </div>
        {/each}
      </div>

      <div class="status">
        <p>Current: Rotation {currentRotation + 1} - {mode.toUpperCase()}</p>
      </div>
    </main>
  </div>
</div>