<script lang="ts">
  import { onMount } from 'svelte';

  interface Tile {
    id: number;
    label: string;
    sub: string;
    color: 'red' | 'dark' | 'mid';
    homeX: number;
    homeY: number;
    x: number;
    y: number;
    rot: number;
    homeRot: number;
    z: number;
    dragging: boolean;
    ox: number;
    oy: number;
    exploded: boolean;
  }

  const DEFS = [
    { label: '3+', sub: 'Years Exp.', color: 'red' },
    { label: 'Svelte', sub: 'Specialist', color: 'dark' },
    { label: 'TypeScript', sub: 'First', color: 'mid' },
    { label: 'React', sub: '+ Next.js', color: 'dark' },
    { label: 'PWA', sub: 'Builder', color: 'mid' },
    { label: 'A11y', sub: 'First', color: 'red' },
    { label: 'E2EE', sub: 'Security', color: 'dark' },
    { label: 'Real-Time', sub: 'Systems', color: 'mid' },
    { label: 'Chrome', sub: 'Extensions', color: 'dark' },
    { label: 'Open', sub: 'Source', color: 'red' },
    { label: 'Vue 3', sub: 'Proficient', color: 'mid' },
    { label: '100', sub: 'Lighthouse', color: 'dark' },
  ] as const;

  const COLS = 4;
  const TW = 160; // tile width
  const TH = 110; // tile height
  const GAP = 12;

  let tiles = $state<Tile[]>([]);
  let maxZ = $state(100);
  let wrap: HTMLElement;
  let ready = $state(false);
  let exploding = $state(false);

  function calcHome(i: number, containerW: number) {
    const col = i % COLS;
    const row = Math.floor(i / COLS);
    const totalW = COLS * TW + (COLS - 1) * GAP;
    const startX = (containerW - totalW) / 2;
    return {
      homeX: startX + col * (TW + GAP),
      homeY: row * (TH + GAP),
      homeRot: 0,
    };
  }

  onMount(() => {
    const r = wrap.getBoundingClientRect();
    tiles = DEFS.map((d, i) => {
      const { homeX, homeY, homeRot } = calcHome(i, r.width);
      return {
        ...d, id: i,
        homeX, homeY, homeRot,
        x: homeX, y: homeY, rot: homeRot,
        z: i + 1, dragging: false, ox: 0, oy: 0, exploded: false,
      };
    });
    ready = true;
  });

  function startDrag(e: MouseEvent | TouchEvent, id: number) {
    e.preventDefault();
    const mx = 'touches' in e ? e.touches[0].clientX : e.clientX;
    const my = 'touches' in e ? e.touches[0].clientY : e.clientY;
    const r = wrap.getBoundingClientRect();
    maxZ++;
    tiles = tiles.map(t =>
      t.id === id ? { ...t, dragging: true, z: maxZ, rot: 4, ox: mx - r.left - t.x, oy: my - r.top - t.y, exploded: true } : t
    );

    const onMove = (me: MouseEvent | TouchEvent) => {
      const x = 'touches' in me ? me.touches[0].clientX : (me as MouseEvent).clientX;
      const y = 'touches' in me ? me.touches[0].clientY : (me as MouseEvent).clientY;
      const rr = wrap.getBoundingClientRect();
      tiles = tiles.map(t => t.id === id ? { ...t, x: x - rr.left - t.ox, y: y - rr.top - t.oy } : t);
    };

    const onUp = () => {
      tiles = tiles.map(t => t.id === id ? { ...t, dragging: false, rot: Math.random() * 24 - 12 } : t);
      window.removeEventListener('mousemove', onMove);
      window.removeEventListener('mouseup', onUp);
      window.removeEventListener('touchmove', onMove);
      window.removeEventListener('touchend', onUp);
    };

    window.addEventListener('mousemove', onMove);
    window.addEventListener('mouseup', onUp);
    window.addEventListener('touchmove', onMove, { passive: false });
    window.addEventListener('touchend', onUp);
  }

  function explode() {
    exploding = true;
    const r = wrap.getBoundingClientRect();
    const cx = r.width / 2;
    const cy = (DEFS.length / COLS) * (TH + GAP) / 2;
    tiles = tiles.map(t => {
      const dx = t.homeX + TW / 2 - cx;
      const dy = t.homeY + TH / 2 - cy;
      const dist = Math.sqrt(dx * dx + dy * dy) || 1;
      const force = 180 + Math.random() * 120;
      return {
        ...t,
        x: t.homeX + (dx / dist) * force,
        y: t.homeY + (dy / dist) * force,
        rot: Math.random() * 40 - 20,
        exploded: true,
      };
    });
    setTimeout(() => exploding = false, 600);
  }

  function reset() {
    tiles = tiles.map(t => ({
      ...t, x: t.homeX, y: t.homeY, rot: 0, exploded: false,
    }));
  }

  const gridRows = Math.ceil(DEFS.length / COLS);
  const gridH = gridRows * TH + (gridRows - 1) * GAP;
</script>

<div class="puzzle-wrap">
  <div class="puzzle-board" bind:this={wrap} style="height:{gridH + 60}px" aria-label="Interactive puzzle tiles" role="region">
    {#if ready}
      {#each tiles as t (t.id)}
        <div
          class="tile tile-{t.color}"
          class:dragging={t.dragging}
          class:exploded={t.exploded}
          style="
            left:{t.x}px; top:{t.y}px; z-index:{t.z};
            transform: rotate({t.rot}deg) scale({t.dragging ? 1.08 : 1});
            transition: {t.dragging
              ? 'transform 0.08s ease'
              : 'left 0.65s cubic-bezier(0.34,1.56,0.64,1), top 0.65s cubic-bezier(0.34,1.56,0.64,1), transform 0.5s cubic-bezier(0.34,1.56,0.64,1), box-shadow 0.3s ease'};
          "
          onmousedown={e => startDrag(e, t.id)}
          ontouchstart={e => startDrag(e, t.id)}
          role="button"
          tabindex="0"
          aria-label="Tile: {t.label} {t.sub}"
          data-cursor="DRAG"
        >
          <span class="tile-label">{t.label}</span>
          <span class="tile-sub">{t.sub}</span>
        </div>
      {/each}
    {/if}
  </div>

  <div class="puzzle-controls">
    <button class="ctrl-btn ctrl-explode" onclick={explode} disabled={exploding} data-cursor="BOOM" aria-label="Explode tiles">
      💥 Scatter
    </button>
    <button class="ctrl-btn ctrl-reset" onclick={reset} data-cursor="SNAP" aria-label="Reset tiles to home position">
      ↩ Reset
    </button>
  </div>
</div>

<style>
  .puzzle-wrap { margin-top: 48px; }

  .puzzle-board {
    position: relative;
    width: 100%;
    overflow: visible;
    user-select: none;
  }

  .tile {
    position: absolute;
    width: 160px;
    height: 110px;
    border-radius: 14px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 4px;
    padding: 16px 18px;
    touch-action: none;
    will-change: transform, left, top;
  }

  .tile-dark {
    background: #0a0a0a;
    color: #f0f0eb;
    box-shadow: 3px 3px 0 rgba(0,0,0,0.3), 0 8px 24px rgba(0,0,0,0.15);
  }
  .tile-mid {
    background: #1e1e1e;
    color: #f0f0eb;
    box-shadow: 3px 3px 0 rgba(0,0,0,0.25), 0 8px 24px rgba(0,0,0,0.12);
  }
  .tile-red {
    background: #e8003a;
    color: #fff;
    box-shadow: 3px 3px 0 rgba(180,0,40,0.4), 0 8px 24px rgba(232,0,58,0.25);
  }

  .tile:hover { cursor: grab; }
  .tile.dragging { cursor: grabbing; box-shadow: 0 28px 56px rgba(0,0,0,0.4) !important; }
  .tile.exploded:not(.dragging) { cursor: grab; }

  .tile-label {
    font-size: 20px;
    font-weight: 900;
    letter-spacing: -0.03em;
    line-height: 1;
  }
  .tile-sub {
    font-size: 10px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    opacity: 0.6;
  }

  /* Controls */
  .puzzle-controls {
    display: flex;
    gap: 12px;
    margin-top: 32px;
    flex-wrap: wrap;
  }

  .ctrl-btn {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 12px 24px;
    border-radius: 10px;
    font-family: inherit;
    font-size: 12px;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.08em;
    border: 2px solid transparent;
    transition: transform 0.25s cubic-bezier(0.34,1.56,0.64,1), opacity 0.2s ease;
  }
  .ctrl-btn:hover:not(:disabled) { transform: translateY(-3px); }
  .ctrl-btn:disabled { opacity: 0.4; }

  .ctrl-explode {
    background: #0a0a0a;
    color: #f0f0eb;
  }
  .ctrl-explode:hover:not(:disabled) {
    box-shadow: 0 10px 24px rgba(0,0,0,0.2);
  }

  .ctrl-reset {
    background: transparent;
    color: #0a0a0a;
    border-color: rgba(0,0,0,0.15);
  }
  .ctrl-reset:hover {
    background: #0a0a0a;
    color: #f0f0eb;
    border-color: #0a0a0a;
  }

  /* Focus */
  .tile:focus-visible {
    outline: 2px solid #e8003a;
    outline-offset: 3px;
  }

  /* Mobile — shrink tiles */
  @media (max-width: 640px) {
    .tile { width: 130px; height: 90px; padding: 12px 14px; border-radius: 10px; }
    .tile-label { font-size: 16px; }
  }
</style>
