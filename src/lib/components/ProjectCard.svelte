<script lang="ts">
  interface Project {
    id: number;
    title: string;
    desc: string;
    tags: string[];
    category: string;
    live?: string;
    github?: string;
    year: string;
    featured?: boolean;
  }

  let { project }: { project: Project } = $props();

  function tilt(node: HTMLElement) {
    const move = (e: MouseEvent) => {
      const r = node.getBoundingClientRect();
      const x = ((e.clientX - r.left) / r.width - 0.5) * 14;
      const y = ((e.clientY - r.top) / r.height - 0.5) * -14;
      node.style.transform = `perspective(900px) rotateX(${y}deg) rotateY(${x}deg) translateY(-6px) scale(1.02)`;
      const shine = node.querySelector('.shine') as HTMLElement;
      if (shine) {
        shine.style.background = `radial-gradient(circle at ${((e.clientX - r.left) / r.width) * 100}% ${((e.clientY - r.top) / r.height) * 100}%, rgba(255,255,255,0.08) 0%, transparent 70%)`;
      }
    };
    const leave = () => {
      node.style.transform = '';
      const shine = node.querySelector('.shine') as HTMLElement;
      if (shine) shine.style.background = '';
    };
    node.addEventListener('mousemove', move);
    node.addEventListener('mouseleave', leave);
    return { destroy() { node.removeEventListener('mousemove', move); node.removeEventListener('mouseleave', leave); } };
  }
</script>

<article class="project-card" class:featured={project.featured} use:tilt data-cursor="VIEW" aria-label="Project: {project.title}">
  <div class="shine"></div>
  <div class="card-top">
    <span class="year">{project.year}</span>
    {#if project.featured}<span class="featured-badge">Featured</span>{/if}
  </div>
  <h3 class="project-title">{project.title}</h3>
  <p class="project-desc">{project.desc}</p>
  <div class="tags">
    {#each project.tags as tag}
      <span class="tag">{tag}</span>
    {/each}
  </div>
  <div class="card-links">
    {#if project.live}
      <a href={project.live} target="_blank" rel="noopener noreferrer" class="link-btn" data-cursor="DEMO" aria-label="Live demo for {project.title}">Live →</a>
    {/if}
    {#if project.github}
      <a href={project.github} target="_blank" rel="noopener noreferrer" class="link-btn link-ghost" data-cursor="CODE" aria-label="GitHub repo for {project.title}">GitHub</a>
    {/if}
  </div>
</article>

<style>
  .project-card {
    position: relative;
    background: #ffffff;
    border: 1.5px solid rgba(0,0,0,0.08);
    border-radius: 16px;
    padding: 28px;
    display: flex;
    flex-direction: column;
    gap: 14px;
    transition: transform 0.2s ease, box-shadow 0.3s ease, border-color 0.3s ease;
    transform-style: preserve-3d;
    will-change: transform;
    overflow: hidden;
  }
  .project-card:hover { border-color: rgba(232,0,58,0.3); box-shadow: 0 24px 48px rgba(0,0,0,0.1), 0 0 0 1px rgba(232,0,58,0.12); }
  .project-card.featured { border-color: rgba(232,0,58,0.2); background: linear-gradient(135deg, #fff, #fafafa); }
  .shine { position: absolute; inset: 0; border-radius: 16px; pointer-events: none; transition: background 0.1s ease; }
  .card-top { display: flex; align-items: center; justify-content: space-between; }
  .year { font-size: 11px; font-weight: 600; color: #888; letter-spacing: 0.1em; text-transform: uppercase; }
  .featured-badge { font-size: 9px; font-weight: 800; text-transform: uppercase; letter-spacing: 0.12em; color: var(--accent); border: 1px solid rgba(232,0,58,0.4); padding: 2px 8px; border-radius: 4px; }
  .project-title { font-size: 22px; font-weight: 800; line-height: 1.2; letter-spacing: -0.02em; color: #0a0a0a; }
  .project-desc { font-size: 13px; line-height: 1.65; color: #555; flex: 1; }
  .tags { display: flex; flex-wrap: wrap; gap: 6px; }
  .tag { font-size: 10px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.08em; color: var(--accent2); background: rgba(0,212,255,0.08); border: 1px solid rgba(0,212,255,0.15); padding: 3px 8px; border-radius: 100px; }
  .card-links { display: flex; gap: 10px; margin-top: auto; }
  .link-btn { font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: 0.08em; padding: 8px 16px; border-radius: 8px; text-decoration: none; background: var(--accent); color: #fff; transition: transform 0.2s var(--ease-spring), background 0.2s ease; }
  .link-btn:hover { transform: translateY(-2px); background: #c4002f; }
  .link-ghost { background: transparent; color: var(--text-muted); border: 1px solid var(--border); }
  .link-ghost:hover { background: var(--surface); color: var(--text); }
</style>
