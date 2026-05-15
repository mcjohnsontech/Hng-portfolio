<script lang="ts">
  import { onMount } from 'svelte';

  let { question = $bindable('') }: { question?: string } = $props();

  const blackCards = [
    "I got 99 problems but _____ ain't one.",
    "What's there a ton of in heaven?",
    "What's the next Happy Meal toy?",
    "What would grandma find disturbing, yet oddly charming?",
    "Lifetime presents: _____: The _____ Story.",
    "What's that smell?",
    "In M. Night Shyamalan's new movie, the main character must deal with _____ all along.",
    "What gives me uncontrollable gas?",
    "Dear Abby, I'm having some trouble with _____ and I was wondering if you could help.",
  ];

  let currentIndex = $state(0);
  let isAnimating = $state(false);
  let visible = $state(true);

  onMount(() => {
    question = blackCards[0];
    const interval = setInterval(() => {
      isAnimating = true;
      visible = false;
      setTimeout(() => {
        currentIndex = (currentIndex + 1) % blackCards.length;
        question = blackCards[currentIndex];
        visible = true;
        setTimeout(() => { isAnimating = false; }, 400);
      }, 300);
    }, 4000);
    return () => clearInterval(interval);
  });
</script>

<div class="rotating-card" class:hidden={!visible}>
  <div class="card-logo-top">Cards Against Humanity</div>
  <p class="card-question">{question || blackCards[0]}</p>
  <div class="card-meta">
    <span class="pick-badge">Pick 1</span>
  </div>
</div>

<style>
  .rotating-card {
    background: #111;
    color: #f8f8f3;
    border-radius: 16px;
    padding: 28px 24px;
    width: 240px;
    height: 320px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    box-shadow: 8px 8px 0 rgba(0,0,0,0.3), 0 20px 60px rgba(0,0,0,0.4);
    transition: opacity 0.3s ease, transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
    will-change: transform, opacity;
  }

  .rotating-card.hidden {
    opacity: 0;
    transform: translateY(-10px) scale(0.97);
  }

  .card-logo-top {
    font-size: 9px;
    font-weight: 800;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    opacity: 0.6;
  }

  .card-question {
    font-size: 18px;
    font-weight: 800;
    line-height: 1.35;
    letter-spacing: -0.02em;
    flex: 1;
    display: flex;
    align-items: center;
    padding: 20px 0;
  }

  .card-meta {
    display: flex;
    justify-content: flex-end;
  }

  .pick-badge {
    font-size: 9px;
    font-weight: 800;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    border: 1.5px solid rgba(248,248,243,0.4);
    padding: 3px 8px;
    border-radius: 4px;
    opacity: 0.7;
  }
</style>
