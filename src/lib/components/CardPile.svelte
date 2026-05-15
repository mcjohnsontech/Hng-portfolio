<script lang="ts">
  import { onMount } from 'svelte';

  interface Card { id:number; text:string; type:'black'|'white'; x:number; y:number; rot:number; z:number; dragging:boolean; ox:number; oy:number; }

  const defs: { text:string; type:'black'|'white' }[] = [
    { text:'Responsiveness', type:'white' },
    { text:'React 19', type:'white' },
    { text:'Tailwind CSS', type:'white' },
    { text:'SEO', type:'white' },
    { text:'Real-Time Data', type:'white' },
    { text:'Immersive UI', type:'white' },
    { text:'Reactive Systems', type:'white' },
    { text:'SvelteKit', type:'white' },
    { text:'TypeScript', type:'white' },
    { text:'CSS Animations', type:'white' },
    { text:'Web Accessibility', type:'white' },
    { text:'Performance', type:'white' },
    { text:'Component Design', type:'white' },
    { text:'Dark Mode', type:'white' },
    { text:'Micro-interactions', type:'white' },
    { text:'Progressive Enhancement', type:'white' },
    { text:'State Management', type:'white' },
    { text:'Web APIs', type:'white' },
    { text:'Frameworks Architecture.', type:'black' },
    { text:'Responsive Design Systems.', type:'black' },
    { text:'What makes a great UX?', type:'black' },
    { text:'Ship fast. Ship right.', type:'black' },
  ];

  // Grid positions as [xRatio, yRatio] evenly covering the container
  const GRID = [
    [0.00,0.00],[0.28,0.01],[0.55,0.00],[0.80,0.02],
    [0.04,0.22],[0.32,0.20],[0.60,0.21],[0.83,0.20],
    [0.01,0.44],[0.29,0.42],[0.57,0.43],[0.81,0.42],
    [0.06,0.65],[0.34,0.64],[0.62,0.65],[0.84,0.63],
    [0.10,0.82],[0.38,0.81],[0.65,0.82],[0.86,0.80],
    [0.18,0.12],[0.48,0.55],
  ];

  const CW = 130; const CH = 175;

  let cards = $state<Card[]>([]);
  let maxZ = $state(30);
  let wrap: HTMLElement;
  let ready = $state(false);
  let isMobile = $state(false);

  onMount(() => {
    isMobile = window.innerWidth < 960;
    if (isMobile) {
      // compact pile - just 5 cards
      const r = wrap.getBoundingClientRect();
      cards = defs.slice(0,5).map((d,i) => ({
        ...d, id:i,
        x: r.width/2 - CW/2 + (i-2)*22 + (Math.random()*20-10),
        y: r.height/2 - CH/2 + (Math.random()*20-10),
        rot: Math.random()*16-8, z:i+1, dragging:false, ox:0, oy:0
      }));
    } else {
      const r = wrap.getBoundingClientRect();
      const W = r.width, H = r.height;
      const maxX = W - CW, maxY = H - CH;
      cards = defs.map((d,i) => {
        const [gx,gy] = GRID[i] || [Math.random()*0.8, Math.random()*0.8];
        return {
          ...d, id:i,
          x: Math.max(0, Math.min(maxX, gx * maxX + (Math.random()*30-15))),
          y: Math.max(0, Math.min(maxY, gy * maxY + (Math.random()*30-15))),
          rot: Math.random()*20-10, z:i+1, dragging:false, ox:0, oy:0
        };
      });
    }
    ready = true;
  });

  function startDrag(e: MouseEvent|TouchEvent, id: number) {
    e.preventDefault();
    const mx = 'touches' in e ? e.touches[0].clientX : e.clientX;
    const my = 'touches' in e ? e.touches[0].clientY : e.clientY;
    const r = wrap.getBoundingClientRect();
    maxZ++;
    cards = cards.map(c => c.id===id ? {...c,dragging:true,z:maxZ,rot:0,ox:mx-r.left-c.x,oy:my-r.top-c.y} : c);
    const onMove = (me: MouseEvent|TouchEvent) => {
      const x='touches' in me?me.touches[0].clientX:(me as MouseEvent).clientX;
      const y='touches' in me?me.touches[0].clientY:(me as MouseEvent).clientY;
      const rr=wrap.getBoundingClientRect();
      cards=cards.map(c=>c.id===id?{...c,x:x-rr.left-c.ox,y:y-rr.top-c.oy}:c);
    };
    const onUp=()=>{
      cards=cards.map(c=>c.id===id?{...c,dragging:false,rot:Math.random()*18-9}:c);
      window.removeEventListener('mousemove',onMove); window.removeEventListener('mouseup',onUp);
      window.removeEventListener('touchmove',onMove); window.removeEventListener('touchend',onUp);
    };
    window.addEventListener('mousemove',onMove); window.addEventListener('mouseup',onUp);
    window.addEventListener('touchmove',onMove,{passive:false}); window.addEventListener('touchend',onUp);
  }
</script>

<div class="pile" bind:this={wrap} aria-label="Draggable skills cards" role="region">
  {#if ready}
    {#each cards as c (c.id)}
      <div
        class="card card-{c.type}"
        class:dragging={c.dragging}
        style="left:{c.x}px;top:{c.y}px;z-index:{c.z};transform:rotate({c.rot}deg) scale({c.dragging?1.06:1});transition:{c.dragging?'transform 0.08s ease':'transform 0.5s cubic-bezier(0.34,1.56,0.64,1),box-shadow 0.3s ease'}"
        onmousedown={e=>startDrag(e,c.id)}
        ontouchstart={e=>startDrag(e,c.id)}
        role="button" tabindex="0"
        aria-label="{c.text}"
        data-cursor="DRAG"
      >
        {#if c.type==='black'}
          <span class="logo">McJohnson</span>
          <span class="term">{c.text}</span>
        {:else}
          <span class="term">{c.text}</span>
          <span class="logo-bt">McJohnson</span>
        {/if}
      </div>
    {/each}
  {/if}
</div>

<style>
  .pile { position:relative; width:100%; height:100%; overflow:visible; }

  .card {
    position:absolute; width:130px; height:175px; border-radius:10px;
    user-select:none; touch-action:none; will-change:transform;
    display:flex; flex-direction:column; justify-content:space-between;
    padding:12px;
  }
  .card-white {
    background:#f5f5f0; color:#111;
    box-shadow:2px 2px 0 rgba(0,0,0,0.1), 0 6px 20px rgba(0,0,0,0.1);
  }
  .card-black {
    background:#111; color:#f5f5f0;
    box-shadow:2px 2px 0 rgba(0,0,0,0.4), 0 8px 24px rgba(0,0,0,0.35);
  }
  .card:hover { box-shadow:0 24px 48px rgba(0,0,0,0.4)!important; }
  .card.dragging { box-shadow:0 32px 64px rgba(0,0,0,0.5)!important; }

  .logo, .logo-bt {
    font-size:6.5px; font-weight:800; text-transform:uppercase;
    letter-spacing:0.08em; opacity:0.4; line-height:1.3;
  }
  .term {
    font-size:11.5px; font-weight:800; line-height:1.35;
    letter-spacing:-0.01em;
  }
</style>
