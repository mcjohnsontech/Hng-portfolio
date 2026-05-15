<script lang="ts">
  import { onMount } from 'svelte';

  let cx = $state(0), cy = $state(0), tx = $state(0), ty = $state(0);
  let clicking = $state(false), hovering = $state(false), visible = $state(false);
  let label = $state('');

  onMount(() => {
    let raf: number;
    const move = (e: MouseEvent) => { cx = e.clientX; cy = e.clientY; visible = true; };
    const trail = () => { tx += (cx - tx) * 0.12; ty += (cy - ty) * 0.12; raf = requestAnimationFrame(trail); };
    trail();
    const hover = (e: MouseEvent) => {
      const el = (e.target as HTMLElement).closest('[data-cursor]');
      hovering = !!el; label = el?.getAttribute('data-cursor') || '';
    };
    window.addEventListener('mousemove', move);
    window.addEventListener('mousemove', hover);
    window.addEventListener('mousedown', () => clicking = true);
    window.addEventListener('mouseup', () => clicking = false);
    return () => { cancelAnimationFrame(raf); window.removeEventListener('mousemove', move); window.removeEventListener('mousemove', hover); };
  });
</script>

{#if visible}
  <div class="trail" class:hovering class:clicking style="transform:translate({tx-20}px,{ty-20}px)">
    {#if label}<span>{label}</span>{/if}
  </div>
  <div class="dot" class:clicking style="transform:translate({cx-4}px,{cy-4}px)"></div>
{/if}

<style>
  .dot { position:fixed;top:0;left:0;width:8px;height:8px;background:#fff;border-radius:50%;pointer-events:none;z-index:9999;mix-blend-mode:difference;will-change:transform;transition:width 0.15s ease,height 0.15s ease; }
  .dot.clicking { width:14px;height:14px; }
  .trail { position:fixed;top:0;left:0;width:40px;height:40px;border:2px solid rgba(255,255,255,0.5);border-radius:50%;pointer-events:none;z-index:9998;mix-blend-mode:difference;display:flex;align-items:center;justify-content:center;will-change:transform;transition:width 0.3s ease,height 0.3s ease,border-color 0.3s ease,background 0.3s ease; }
  .trail.hovering { width:68px;height:68px;border-color:#e8003a;background:rgba(232,0,58,0.1); }
  .trail.clicking { width:28px;height:28px; }
  .trail span { font-size:7px;font-weight:800;text-transform:uppercase;letter-spacing:0.06em;color:#fff;white-space:nowrap; }
</style>
