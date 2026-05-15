<script lang="ts">
  import { onMount } from 'svelte';

  const PROMPT = 'portfolio@dev:~$';
  const COMMANDS: Record<string, string> = {
    help:     '  Available: <span class="c-accent">about</span>, <span class="c-accent">skills</span>, <span class="c-accent">projects</span>, <span class="c-accent">contact</span>, <span class="c-accent">clear</span>',
    about:    '  Frontend Engineer. I build fast, animated, accessible web experiences. 3+ years shipping production apps.',
    skills:   '  Svelte · TypeScript · React · Node.js · CSS/GSAP · WebGL · Figma · Vite · PostgreSQL',
    projects: '  → <span class="c-accent">Luminary</span> — Real-time collab design tool (2024)\n  → <span class="c-accent">Orbitask</span> — Animated task management SPA (2024)\n  → <span class="c-accent">Prism UI</span> — Open-source component library (2023)',
    contact:  '  Email: <span class="c-accent">hello@devportfolio.dev</span> | GitHub: <span class="c-accent">github.com/devportfolio</span>',
    clear:    '__CLEAR__',
  };

  interface Line { type: 'in' | 'out' | 'err'; html: string; }

  let lines = $state<Line[]>([
    { type: 'out', html: '<span class="c-muted">Welcome to the portfolio terminal. Type <span class="c-accent">help</span> for commands.</span>' },
  ]);
  let input = $state('');
  let inputEl: HTMLInputElement;
  let containerEl: HTMLElement;

  function submit() {
    const cmd = input.trim().toLowerCase();
    if (!cmd) return;
    lines = [...lines, { type: 'in', html: `${PROMPT} ${cmd}` }];
    const result = COMMANDS[cmd];
    if (result === '__CLEAR__') {
      lines = [];
    } else if (result) {
      lines = [...lines, { type: 'out', html: result }];
    } else {
      lines = [...lines, { type: 'err', html: `  Command not found: <span class="c-accent">${cmd}</span>. Try <span class="c-accent">help</span>.` }];
    }
    input = '';
    setTimeout(() => { if (containerEl) containerEl.scrollTop = containerEl.scrollHeight; }, 10);
  }
</script>

<div class="terminal" aria-label="Interactive terminal" role="region">
  <div class="term-titlebar">
    <span class="dot" style="background:#ff5f57"></span>
    <span class="dot" style="background:#ffbd2e"></span>
    <span class="dot" style="background:#28c840"></span>
    <span class="term-title">portfolio@dev — terminal</span>
  </div>
  <div class="term-body" bind:this={containerEl}>
    {#each lines as line}
      <div class="line line-{line.type}">
        <!-- eslint-disable-next-line svelte/no-at-html-tags -->
        {@html line.html}
      </div>
    {/each}
    <div class="input-row">
      <span class="prompt">{PROMPT}</span>
      <input
        bind:this={inputEl}
        bind:value={input}
        onkeydown={(e) => e.key === 'Enter' && submit()}
        class="term-input"
        aria-label="Terminal command input"
        autocomplete="off"
        spellcheck="false"
        id="terminal-input"
      />
    </div>
  </div>
</div>

<style>
  .terminal { background: #0d0d0d; border: 1px solid rgba(255,255,255,0.1); border-radius: 14px; overflow: hidden; font-family: 'Menlo', 'Monaco', 'Courier New', monospace; box-shadow: 0 40px 80px rgba(0,0,0,0.6); max-width: 680px; margin: 0 auto; }
  .term-titlebar { background: #1c1c1c; padding: 12px 16px; display: flex; align-items: center; gap: 8px; border-bottom: 1px solid rgba(255,255,255,0.06); }
  .dot { width: 12px; height: 12px; border-radius: 50%; }
  .term-title { font-size: 11px; color: rgba(255,255,255,0.3); margin-left: 8px; letter-spacing: 0.05em; }
  .term-body { padding: 20px; min-height: 240px; max-height: 340px; overflow-y: auto; display: flex; flex-direction: column; gap: 6px; }
  .term-body::-webkit-scrollbar { width: 3px; }
  .term-body::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.1); }
  .line { font-size: 13px; line-height: 1.6; white-space: pre-wrap; }
  .line-in { color: #f0f0eb; }
  .line-out { color: rgba(240,240,235,0.7); }
  .line-err { color: #ff6b6b; }
  :global(.c-accent) { color: #e8003a; font-weight: 600; }
  :global(.c-muted) { color: rgba(240,240,235,0.4); }
  .input-row { display: flex; align-items: center; gap: 8px; margin-top: 4px; }
  .prompt { font-size: 13px; color: #e8003a; white-space: nowrap; }
  .term-input { flex: 1; background: none; border: none; outline: none; font-family: inherit; font-size: 13px; color: #f0f0eb; caret-color: #e8003a; cursor: none !important; }
</style>
