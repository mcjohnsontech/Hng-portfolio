<script lang="ts">
  import './layout.css';
  import { onMount } from 'svelte';

  let { children } = $props();
  let theme = $state('dark');

  onMount(() => {
    const saved = localStorage.getItem('theme') || 'dark';
    theme = saved;
    document.documentElement.setAttribute('data-theme', saved === 'light' ? 'light' : '');
  });

  // Expose theme toggle globally via context
  function toggleTheme() {
    theme = theme === 'dark' ? 'light' : 'dark';
    document.documentElement.setAttribute('data-theme', theme === 'light' ? 'light' : '');
    localStorage.setItem('theme', theme);
  }
</script>

<svelte:head>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous" />
  <link href="https://fonts.googleapis.com/css2?family=Inter:ital,wght@0,300;0,400;0,500;0,700;0,900;1,400&display=swap" rel="stylesheet" />
</svelte:head>

<!-- Skip to main content for accessibility -->
<a href="#main" class="skip-link">Skip to main content</a>

{@render children()}

<style>
  .skip-link {
    position: fixed;
    top: -100px;
    left: 20px;
    z-index: 99999;
    background: var(--accent);
    color: #fff;
    padding: 10px 20px;
    border-radius: 6px;
    font-size: 13px;
    font-weight: 700;
    text-decoration: none;
    transition: top 0.2s ease;
  }
  .skip-link:focus { top: 20px; }
</style>
