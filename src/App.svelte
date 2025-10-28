<script>
  import { onMount, onDestroy } from 'svelte';

  const pads = [
    { key: 'Q', id: 'Heater-1', name: 'Heater 1', src: '/sounds/1.mp3' },
    { key: 'W', id: 'Heater-2', name: 'Heater 2', src: '/sounds/2.mp3' },
    { key: 'E', id: 'Heater-3', name: 'Heater 3', src: '/sounds/3.mp3' },
    { key: 'A', id: 'Heater-4', name: 'Heater 4', src: '/sounds/4.mp3' },
    { key: 'S', id: 'Clap',     name: 'Clap',     src: '/sounds/5.mp3' },
    { key: 'D', id: 'Open-HH',  name: 'Open HH',  src: '/sounds/6.mp3' },
    { key: 'Z', id: "Kick-n'-Hat", name: "Kick n' Hat", src: '/sounds/7.mp3' },
    { key: 'X', id: 'Kick',     name: 'Kick',     src: '/sounds/8.mp3' },
    { key: 'C', id: 'Closed-HH',name: 'Closed HH',src: '/sounds/9.mp3' }
  ];

  let displayText = '';
  const audioMap = new Map();

  function playPad(pad) {
    const audio = audioMap.get(pad.key);
    if (!audio) return;
    audio.currentTime = 0;
    audio.play();
    displayText = pad.name;
    const padEl = document.getElementById(pad.id);
    if (padEl) {
      padEl.classList.add('active');
      setTimeout(() => padEl.classList.remove('active'), 100);
    }
  }

  function handleClick(event, pad) {
    playPad(pad);
  }

  function handleKeydown(e) {
    const key = e.key.toUpperCase();
    const pad = pads.find(p => p.key === key);
    if (pad) {
      // US #6: pressionar tecla aciona o pad correspondente
      playPad(pad);
    }
  }

  onMount(() => {
    // Preencher audioMap e garantir que os elementos estão associados
    pads.forEach(pad => {
      const audio = document.getElementById(pad.key);
      if (audio) audioMap.set(pad.key, audio);
    });
    window.addEventListener('keydown', handleKeydown);
  });

  onDestroy(() => {
    window.removeEventListener('keydown', handleKeydown);
  });
</script>

<main id="drum-machine">
  <h1>Drum Machine</h1>

  <div id="display" class="display" aria-live="polite">
    {displayText || 'Ready'}
  </div>

  <div class="pads">
    {#each pads as pad}
      <button
        class="drum-pad"
        id={pad.id}
        on:click={(e) => handleClick(e, pad)}
        tabindex="0"
        aria-label={pad.name}
      >
        {pad.key}
        <audio
          class="clip"
          id={pad.key}
          src={pad.src}
          preload="auto"
        ></audio>
  </button>
    {/each}
  </div>
</main>

<style>
  main {
    font-family: system-ui, sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 2rem;
    gap: 1rem;
  }

  .display {
    width: 320px;
    height: 48px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 600;
    background: #0b0b0b;
    border-radius: 6px;
    text-decoration: underline;
  }

  .pads {
    display: grid;
    grid-template-columns: repeat(3, 100px);
    gap: 12px;
    margin-top: 12px;
  }

  .drum-pad {
    width: 100px;
    height: 100px;
    background-color: #0f0f0f;
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.6rem;
    border-radius: 8px;
    cursor: pointer;
    user-select: none;
    transition: transform 60ms, background 120ms;
  }

  .drum-pad:active,
  .drum-pad.active {
    transform: translateY(4px);
    box-shadow: 0 0 0 rgba(0,0,0,0);
    background: linear-gradient(180deg, #666, #444);
  }

  .drum-pad:focus {
    outline: 3px solid #99f;
  }

  /* estilos do audio invisível */
  .clip {
    display: none;
  }
</style>