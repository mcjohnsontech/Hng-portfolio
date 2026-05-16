<script lang="ts">
  import { onMount } from 'svelte';
  import Cursor from '$lib/components/Cursor.svelte';
  import CardPile from '$lib/components/CardPile.svelte';
  import Terminal from '$lib/components/Terminal.svelte';
  import ContactForm from '$lib/components/ContactForm.svelte';
  import ProjectCard from '$lib/components/ProjectCard.svelte';
  import PuzzleSection from '$lib/components/PuzzleSection.svelte';

  let scrollProgress = $state(0);
  let navScrolled = $state(false);
  let heroVisible = $state(false);
  let theme = $state('dark');
  let activeFilter = $state('All');
  let typedText = $state('');

  const titles = ['Frontend Engineer.','Interaction Designer.','Svelte Specialist.','Performance Obsessive.'];
  let ti = 0, ci = 0, del = false, pause = 0;

  const projects = [
    { id:1, title:'Luminary', desc:'Real-time collaborative design tool with live cursors, WebSocket sync, and infinite canvas. Built for 10k concurrent users.', tags:['Svelte','WebSocket','Canvas API','TypeScript'], category:'App', live:'#', github:'#', year:'2024', featured:true },
    { id:2, title:'Orbitask', desc:'Animated task management SPA. Physics-based drag, card flip transitions, offline-first with IndexedDB.', tags:['SvelteKit','Framer Motion','IndexedDB'], category:'App', live:'#', github:'#', year:'2024', featured:true },
    { id:3, title:'Prism UI', desc:'Open-source accessible component library. Zero deps, 40+ components, full WCAG 2.1 AA compliance.', tags:['Svelte','CSS','Storybook','A11y'], category:'Library', github:'#', year:'2023' },
  ];

  const experiments = [
    { emoji:'🌊', title:'Waveform', desc:'Real-time audio visualizer using Web Audio API and custom WebGL shaders.', tag:'WebGL Experiment' },
    { emoji:'🧠', title:'MotionKit', desc:'Animation utility library — spring physics, scroll timelines, gesture recognition.', tag:'Open Source' },
    { emoji:'🖥️', title:'This Portfolio', desc:'Built with SvelteKit. Animated, accessible, performant. The one you\'re reading now.', tag:'Meta' },
  ];

  const stack = [
    { label:'Primary', items:['Svelte / SvelteKit','TypeScript','CSS & SCSS','Node.js'] },
    { label:'Animation', items:['GSAP','Motion One','CSS Springs','Web Animations API'] },
    { label:'Tooling', items:['Vite','Playwright','Lighthouse','Figma'] },
  ];

  const ticker = ['SVELTE','TYPESCRIPT','PERFORMANCE','ANIMATION','WEBGL','ACCESSIBILITY','SVELTEKIT','CSS MASTERY','FIGMA','NODE.JS','OPEN SOURCE','INTERACTION DESIGN'];

  function reveal(node: HTMLElement) {
    node.style.cssText += ';opacity:0;transform:translateY(32px);transition:opacity 0.7s ease,transform 0.75s cubic-bezier(0.22,1,0.36,1)';
    const check = () => { if (node.getBoundingClientRect().top < window.innerHeight * 0.9) { node.style.opacity='1'; node.style.transform='translateY(0)'; } };
    window.addEventListener('scroll', check, { passive: true });
    setTimeout(check, 100);
    return { destroy: () => window.removeEventListener('scroll', check) };
  }

  function toggleTheme() {
    theme = theme === 'dark' ? 'light' : 'dark';
    document.documentElement.setAttribute('data-theme', theme === 'light' ? 'light' : '');
    localStorage.setItem('theme', theme);
  }

  onMount(() => {
    theme = localStorage.getItem('theme') || 'dark';
    setTimeout(() => heroVisible = true, 80);

    const typeInterval = setInterval(() => {
      const cur = titles[ti];
      if (!del) { typedText = cur.slice(0, ++ci); if (ci === cur.length) { del = true; pause = 28; } }
      else { if (pause-- > 0) return; typedText = cur.slice(0, --ci); if (ci === 0) { del = false; ti = (ti+1) % titles.length; } }
    }, 65);

    const onScroll = () => {
      navScrolled = window.scrollY > 50;
      scrollProgress = (window.scrollY / (document.documentElement.scrollHeight - window.innerHeight)) * 100;
    };
    window.addEventListener('scroll', onScroll, { passive: true });
    return () => { clearInterval(typeInterval); window.removeEventListener('scroll', onScroll); };
  });
</script>

<svelte:head>
  <title>Abiodun Mark – Frontend Engineer</title>
  <meta name="description" content="Frontend engineer portfolio. I build fast, animated, accessible web experiences with Svelte." />
</svelte:head>

<Cursor />
<div class="scroll-bar" style="width:{scrollProgress}%"></div>

<!-- ═══════════════════════════ NAV ═══════════════════════════ -->
<nav class="nav" class:scrolled={navScrolled} aria-label="Main navigation">
  <div class="nav-in">
    <a href="/" class="logo" aria-label="Home">AR<span>.</span></a>
    <div class="nav-links">
      {#each [['Work','#work'],['Approach','#approach'],['Experiments','#experiments'],['Contact','#contact']] as [l,h]}
        <a href={h} data-cursor={l.toUpperCase()}>{l}</a>
      {/each}
    </div>
    <button class="theme-btn" onclick={toggleTheme} aria-label="Toggle {theme === 'dark' ? 'light' : 'dark'} mode" data-cursor="THEME">
      {theme === 'dark' ? '☀' : '☾'}
    </button>
  </div>
</nav>

<!-- ═══════════════════════════ HERO ═══════════════════════════ -->
<section class="hero" aria-label="Introduction" id="hero">
  <div class="hero-in" class:show={heroVisible}>
    <div class="hero-left">
      <p class="eyebrow">Available for work · 2025</p>
      <h1 class="hero-name">Mark<br/><em>Abiodun.</em></h1>
      <p class="hero-sub">
        Frontend engineer. I turn figma files and rough ideas<br/>
        into interfaces people actually enjoy using.
      </p>
      <p class="hero-role-wrap"><span class="hero-role">{typedText}</span><span class="blink">|</span></p>
      <div class="hero-cta">
        <a href="#work" class="btn btn-white" data-cursor="WORK">See the work →</a>
        <a href="/resume.pdf" target="_blank" rel="noopener" class="btn btn-ghost" data-cursor="PDF">Resume ↓</a>
      </div>
      <div class="socials" role="list" aria-label="Social links">
        <a href="https://github.com/mcjohnsontech" target="_blank" rel="noopener" aria-label="GitHub profile" data-cursor="GH">GitHub ↗</a>
        <a href="https://x.com/mcjohnson144" target="_blank" rel="noopener" aria-label="Twitter / X profile" data-cursor="X">Twitter ↗</a>
        <a href="mailto:hello@mcjohnson.dev" aria-label="Send email" data-cursor="MAIL">Email ↗</a>
      </div>
    </div>
    <div class="hero-right" aria-label="Draggable skill tags — try dragging them!">
      <CardPile />
    </div>
  </div>
  <!-- Ticker -->
  <div class="ticker-wrap" aria-hidden="true">
    <div class="ticker">
      {#each [...ticker,...ticker] as t}<span>{t}</span><span class="dot">✦</span>{/each}
    </div>
  </div>
</section>

<!-- ═══════════════════════════ WORK ═══════════════════════════ -->
<section class="section light-section" id="work" aria-labelledby="work-title">
  <div class="in">
    <div class="work-header" use:reveal>
      <div>
        <p class="eyebrow-dark">Step 01</p>
        <h2 id="work-title" class="section-title dark-text">The work.</h2>
        <p class="body-text dark-muted">Production applications spanning AI tooling, security, PWAs, and real-time dashboards. Each project ships with purpose.</p>
        <a href="https://github.com/mcjohnsontech" target="_blank" rel="noopener" class="btn btn-black" data-cursor="MORE">View all on GitHub ↗</a>
      </div>
    </div>
    <div class="work-grid">
      {#each projects as p (p.id)}
        <div use:reveal style="transition-delay:{p.id*0.06}s">
          <ProjectCard project={p} />
        </div>
      {/each}
    </div>
  </div>
</section>

<!-- ═══════════════════════════ APPROACH ═══════════════════════════ -->
<section class="section dark-section" id="approach" aria-labelledby="approach-title">
  <div class="in">
    <div class="approach-layout">
      <div use:reveal>
        <p class="eyebrow">Step 02</p>
        <h2 id="approach-title" class="section-title">How I build.</h2>
        <p class="body-text muted">
          I don't just write code. I think in systems — component hierarchies, animation timing curves, accessibility trees, bundle graphs. Good frontend engineering is invisible: users don't see the work, they just feel the result.
        </p>
        <p class="body-text muted">
          My process starts with performance budgets and ends with Lighthouse scores. In between, there's a lot of careful CSS, reactive state design, and testing on real devices.
        </p>
      </div>
      <div class="approach-right">
        <div class="callout-box" use:reveal>
          <p class="callout-label">What I reach for.</p>
          {#each stack as { label, items }}
            <div class="stack-group">
              <p class="stack-label">{label}</p>
              <ul>
                {#each items as item}<li>{item}</li>{/each}
              </ul>
            </div>
          {/each}
        </div>
        <div use:reveal style="margin-top:32px">
          <Terminal />
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════ EXPERIMENTS ═══════════════════════════ -->
<section class="section light-section" id="experiments" aria-labelledby="exp-title">
  <div class="in">
    <div use:reveal>
      <p class="eyebrow-dark">Step 03</p>
      <h2 id="exp-title" class="section-title dark-text">Things I couldn't<br/>help making.</h2>
    </div>
    <div class="exp-grid">
      {#each experiments as e, i}
        <div class="exp-card" use:reveal style="transition-delay:{i*0.1}s" data-cursor="READ">
          <span class="exp-emoji">{e.emoji}</span>
          <span class="exp-tag">{e.tag}</span>
          <h3 class="exp-title">{e.title}</h3>
          <p class="exp-desc">{e.desc}</p>
        </div>
      {/each}
    </div>
  </div>
</section>

<!-- ═══════════════════════════ CONTACT ═══════════════════════════ -->
<section class="section dark-section contact-section" id="contact" aria-labelledby="contact-title">
  <div class="in contact-in">
    <div use:reveal>
      <h2 id="contact-title" class="contact-title">
        To build something<br/>great together,<br/>send me a message:
      </h2>
    </div>
    <div use:reveal>
      <ContactForm />
      <div class="contact-alt">
        <p>Or reach me directly —</p>
        <a href="mailto:hello@mcjohnson.dev" data-cursor="EMAIL" aria-label="Send direct email">hello@mcjohnson.dev</a>
        <a href="https://cal.com" target="_blank" rel="noopener" data-cursor="BOOK" aria-label="Book a call">Book a call ↗</a>
      </div>
    </div>
  </div>
</section>

<!-- ═══════════════════════════ PUZZLE ═══════════════════════════ -->
<section class="section light-section" id="puzzle" aria-labelledby="puzzle-title">
  <div class="in">
    <div use:reveal>
      <p class="eyebrow-dark">Explore</p>
      <h2 id="puzzle-title" class="section-title dark-text">Break it apart.<br/>Put it back.</h2>
      <p class="body-text dark-muted" style="max-width:480px">Drag the tiles apart to explore what makes me tick. Hit reset to watch them spring back home.</p>
    </div>
    <div use:reveal>
      <PuzzleSection />
    </div>
  </div>
</section>

<!-- ═══════════════════════════ FOOTER ═══════════════════════════ -->
<footer class="footer">
  <div class="in footer-in">
    <div class="footer-left">
      <p class="footer-name">McJohnson</p>
      <p class="footer-role">Frontend Engineer</p>
    </div>
    <nav class="footer-links" aria-label="Footer navigation">
      <a href="https://github.com/mcjohnsontech" target="_blank" rel="noopener" aria-label="GitHub">GitHub</a>
      <a href="https://x.com/mcjohnson144" target="_blank" rel="noopener" aria-label="Twitter / X">Twitter</a>
      <a href="mailto:hello@mcjohnson.dev" aria-label="Email">Email</a>
    </nav>
    <p class="footer-copy">© {new Date().getFullYear()} · Built with SvelteKit</p>
  </div>
</footer>

<style>
  /* ── SCROLL BAR ── */
  .scroll-bar { position:fixed;top:0;left:0;height:3px;background:var(--accent);z-index:9999;pointer-events:none;box-shadow:0 0 10px rgba(232,0,58,0.6);transition:width 0.12s linear; }

  /* ── NAV ── */
  .nav { position:fixed;top:0;left:0;right:0;z-index:200;padding:22px 48px;transition:background 0.4s,padding 0.3s,backdrop-filter 0.4s; }
  .nav.scrolled { background:rgba(10,10,10,0.92);backdrop-filter:blur(20px);padding:14px 48px;box-shadow:0 1px 0 rgba(255,255,255,0.06); }
  .nav-in { max-width:1200px;margin:0 auto;display:flex;align-items:center;gap:40px; }
  .logo { font-size:18px;font-weight:900;letter-spacing:-0.03em;color:#f0f0eb;text-decoration:none;margin-right:auto; }
  .logo span { color:var(--accent); }
  .nav-links { display:flex;gap:28px; }
  .nav-links a { font-size:12px;font-weight:600;text-transform:uppercase;letter-spacing:0.1em;color:#f0f0eb;opacity:0.5;text-decoration:none;transition:opacity 0.2s; }
  .nav-links a:hover { opacity:1; }
  .theme-btn { background:none;border:1px solid rgba(255,255,255,0.15);color:#f0f0eb;border-radius:8px;padding:7px 12px;font-size:14px;transition:border-color 0.2s,transform 0.2s var(--ease-spring); }
  .theme-btn:hover { border-color:var(--accent);transform:scale(1.1); }

  /* ── HERO ── */
  .hero { min-height:100vh;background:#0a0a0a;color:#f0f0eb;display:flex;flex-direction:column;overflow:hidden; }
  .hero-in { flex:1;max-width:1200px;width:100%;margin:0 auto;padding:160px 48px 80px;display:grid;grid-template-columns:1fr 1fr;gap:60px;align-items:center;opacity:0;transform:translateY(24px);transition:opacity 0.9s ease,transform 0.9s ease; }
  .hero-in.show { opacity:1;transform:none; }
  .eyebrow { font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:0.16em;color:var(--accent);margin-bottom:16px; }
  .eyebrow-dark { font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:0.16em;color:var(--accent);margin-bottom:16px; }
  .hero-name { font-size:clamp(64px,9vw,112px);font-weight:900;line-height:0.92;letter-spacing:-0.04em;margin-bottom:24px; }
  .hero-name em { font-style:normal;color:var(--accent); }
  .hero-sub { font-size:16px;line-height:1.7;color:rgba(240,240,235,0.6);max-width:420px;margin-bottom:16px; }
  .hero-role-wrap { font-size:18px;font-weight:600;color:rgba(240,240,235,0.5);margin-bottom:32px;min-height:28px; }
  .hero-role { color:#f0f0eb; }
  .blink { color:var(--accent);animation:blink 1s step-end infinite; }
  @keyframes blink { 0%,100%{opacity:1}50%{opacity:0} }
  .hero-right { height:560px; overflow:visible; }
  .hero-cta { display:flex;gap:12px;flex-wrap:wrap;margin-bottom:28px; }
  .socials { display:flex;gap:24px; }
  .socials a { font-size:12px;font-weight:600;text-transform:uppercase;letter-spacing:0.1em;color:rgba(240,240,235,0.45);text-decoration:none;transition:color 0.2s; }
  .socials a:hover { color:#f0f0eb; }

  /* ── BUTTONS ── */
  .btn { display:inline-flex;align-items:center;padding:13px 28px;border-radius:8px;font-family:inherit;font-size:13px;font-weight:700;text-transform:uppercase;letter-spacing:0.07em;text-decoration:none;border:2px solid transparent;transition:transform 0.25s var(--ease-spring),box-shadow 0.2s,background 0.2s; }
  .btn:hover { transform:translateY(-3px); }
  .btn-white { background:#f0f0eb;color:#0a0a0a; }
  .btn-white:hover { box-shadow:0 12px 28px rgba(240,240,235,0.2); }
  .btn-ghost { background:transparent;color:#f0f0eb;border-color:rgba(240,240,235,0.25); }
  .btn-ghost:hover { border-color:#f0f0eb; }
  .btn-black { background:#0a0a0a;color:#f0f0eb; }
  .btn-black:hover { box-shadow:0 12px 28px rgba(0,0,0,0.3); }

  /* ── TICKER ── */
  .ticker-wrap { overflow:hidden;border-top:1px solid rgba(255,255,255,0.07);padding:14px 0; }
  .ticker { display:flex;white-space:nowrap;width:max-content;animation:tick 26s linear infinite; }
  .ticker span { font-size:11px;font-weight:800;text-transform:uppercase;letter-spacing:0.16em;color:rgba(240,240,235,0.4);padding:0 18px; }
  .ticker .dot { color:var(--accent); }
  @keyframes tick { to { transform:translateX(-50%); } }

  /* ── SECTIONS ── */
  .section { padding:100px 0; }
  .in { max-width:1200px;margin:0 auto;padding:0 48px; }
  .dark-section { background:#0a0a0a;color:#f0f0eb; }
  .light-section { background:#f5f5f0;color:#0a0a0a; }
  .section-title { font-size:clamp(42px,6vw,80px);font-weight:900;line-height:0.97;letter-spacing:-0.04em;margin-bottom:40px; }
  .section-title.dark-text { color:#0a0a0a; }
  .section-title.center { text-align:center;margin-bottom:56px; }
  .body-text { font-size:15px;line-height:1.8;margin-bottom:20px; }
  .muted { color:rgba(240,240,235,0.6); }
  .dark-muted { color:rgba(10,10,10,0.6); }

  /* ── WORK SECTION ── */
  .work-header { margin-bottom:48px; }
  .work-grid { display:grid; grid-template-columns:repeat(2,1fr); gap:20px; }

  /* ── APPROACH SECTION ── */
  .approach-layout { display:grid;grid-template-columns:1fr 1fr;gap:80px;align-items:start; }
  .callout-box { background:#1a1a1a;border:1px solid rgba(255,255,255,0.08);border-radius:16px;padding:32px; }
  .callout-label { font-size:11px;font-weight:800;text-transform:uppercase;letter-spacing:0.14em;color:var(--accent);margin-bottom:24px; }
  .stack-group { margin-bottom:20px; }
  .stack-group:last-child { margin-bottom:0; }
  .stack-label { font-size:10px;font-weight:700;text-transform:uppercase;letter-spacing:0.12em;color:rgba(240,240,235,0.4);margin-bottom:8px; }
  .stack-group ul { list-style:none;display:flex;flex-direction:column;gap:4px; }
  .stack-group li { font-size:14px;font-weight:500;color:rgba(240,240,235,0.8); }

  /* ── EXPERIMENTS ── */
  .exp-grid { display:grid;grid-template-columns:repeat(3,1fr);gap:20px;margin-top:48px; }
  .exp-card { background:#0a0a0a;color:#f0f0eb;border-radius:16px;padding:32px 28px;display:flex;flex-direction:column;gap:12px;transition:transform 0.35s var(--ease-spring),box-shadow 0.3s; }
  .exp-card:hover { transform:translateY(-8px) rotate(-0.4deg);box-shadow:0 28px 56px rgba(0,0,0,0.22); }
  .exp-emoji { font-size:28px; }
  .exp-tag { font-size:9px;font-weight:800;text-transform:uppercase;letter-spacing:0.14em;color:var(--accent);border:1px solid rgba(232,0,58,0.35);padding:3px 8px;border-radius:4px;width:fit-content; }
  .exp-title { font-size:22px;font-weight:800;letter-spacing:-0.02em; }
  .exp-desc { font-size:13px;line-height:1.65;color:rgba(240,240,235,0.55);flex:1; }

  /* ── CONTACT ── */
  .contact-section { padding:100px 0; }
  .contact-in { display:grid;grid-template-columns:1fr 1fr;gap:80px;align-items:start; }
  .contact-title { font-size:clamp(32px,4.5vw,60px);font-weight:900;line-height:1.1;letter-spacing:-0.03em;margin-bottom:0; }
  .contact-alt { margin-top:28px;display:flex;flex-direction:column;gap:10px; }
  .contact-alt p { font-size:12px;font-weight:600;text-transform:uppercase;letter-spacing:0.1em;color:rgba(240,240,235,0.35); }
  .contact-alt a { font-size:14px;font-weight:600;color:rgba(240,240,235,0.6);text-decoration:none;transition:color 0.2s; }
  .contact-alt a:hover { color:var(--accent); }

  /* ── FOOTER ── */
  .footer { background:#0a0a0a;border-top:1px solid rgba(255,255,255,0.07);padding:40px 0; }
  .footer-in { display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:20px; }
  .footer-left { display:flex;flex-direction:column;gap:2px; }
  .footer-name { font-size:13px;font-weight:800;text-transform:uppercase;letter-spacing:0.1em;color:#f0f0eb; }
  .footer-role { font-size:11px;color:rgba(240,240,235,0.4); }
  .footer-links { display:flex;gap:24px; }
  .footer-links a { font-size:11px;font-weight:600;text-transform:uppercase;letter-spacing:0.08em;color:rgba(240,240,235,0.4);text-decoration:none;transition:color 0.2s; }
  .footer-links a:hover { color:var(--accent); }
  .footer-copy { font-size:11px;color:rgba(240,240,235,0.25); }

  /* ── FOCUS ── */
  :global(a:focus-visible),:global(button:focus-visible) { outline:2px solid var(--accent);outline-offset:3px;border-radius:4px; }

  /* ── RESPONSIVE ── */
  @media (max-width:960px) {
    .hero-in { grid-template-columns:1fr;padding:130px 24px 60px;gap:40px; }
    .hero-right { height:300px; overflow:hidden; }
    .work-grid { grid-template-columns:1fr; }
    .approach-layout,.contact-in { grid-template-columns:1fr;gap:40px; }
    .exp-grid { grid-template-columns:1fr 1fr; }
    .nav { padding:16px 24px; }
    .nav.scrolled { padding:12px 24px; }
    .in { padding:0 24px; }
  }
  @media (max-width:600px) {
    .work-grid { grid-template-columns:1fr; }
    .exp-grid { grid-template-columns:1fr; }
    .nav-links { display:none; }
    .footer-in { flex-direction:column;text-align:center;align-items:center; }
  }
</style>
