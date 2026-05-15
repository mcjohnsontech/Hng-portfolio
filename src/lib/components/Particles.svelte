<script lang="ts">
  import { onMount } from 'svelte';

  interface Particle {
    id: number;
    x: number;
    y: number;
    size: number;
    speed: number;
    opacity: number;
    delay: number;
    color: string;
  }

  const colors = ['#e8003a', '#ffffff', '#ffd700'];
  let particles: Particle[] = Array.from({ length: 28 }, (_, i) => ({
    id: i,
    x: Math.random() * 100,
    y: Math.random() * 100,
    size: Math.random() * 4 + 2,
    speed: Math.random() * 18 + 12,
    opacity: Math.random() * 0.3 + 0.05,
    delay: -(Math.random() * 20),
    color: colors[Math.floor(Math.random() * colors.length)],
  }));
</script>

<div class="particles" aria-hidden="true">
  {#each particles as p (p.id)}
    <div
      class="particle"
      style="
        left: {p.x}%;
        width: {p.size}px;
        height: {p.size}px;
        background: {p.color};
        opacity: {p.opacity};
        animation-duration: {p.speed}s;
        animation-delay: {p.delay}s;
      "
    ></div>
  {/each}
</div>

<style>
  .particles {
    position: absolute;
    inset: 0;
    overflow: hidden;
    pointer-events: none;
    z-index: 0;
  }

  .particle {
    position: absolute;
    bottom: -20px;
    border-radius: 50%;
    animation: float-up linear infinite;
  }

  @keyframes float-up {
    0%   { transform: translateY(0) translateX(0) scale(1); opacity: var(--op, 0.1); }
    25%  { transform: translateY(-25vh) translateX(20px) scale(1.1); }
    50%  { transform: translateY(-50vh) translateX(-15px) scale(0.9); }
    75%  { transform: translateY(-75vh) translateX(10px) scale(1.05); }
    100% { transform: translateY(-105vh) translateX(0) scale(0.8); opacity: 0; }
  }
</style>
