<script lang="ts">
  let name = $state('');
  let email = $state('');
  let message = $state('');
  let submitted = $state(false);
  let loading = $state(false);
  let serverError = $state('');
  let errors = $state<Record<string,string>>({});

  // Sanitise string — strip HTML tags
  const clean = (s: string) => s.replace(/<[^>]*>/g, '').trim();

  function validate() {
    const e: Record<string,string> = {};
    if (!clean(name)) e.name = 'Name is required.';
    if (!clean(email) || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(clean(email))) e.email = 'Valid email required.';
    if (clean(message).length < 10) e.message = 'Message must be at least 10 characters.';
    errors = e;
    return Object.keys(e).length === 0;
  }

  async function submit(e: Event) {
    e.preventDefault();
    serverError = '';
    if (!validate()) return;
    loading = true;

    try {
      const res = await fetch('https://api.web3forms.com/submit', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json', Accept: 'application/json' },
        body: JSON.stringify({
          // Replace with your key from https://web3forms.com (free, just enter your email)
          access_key: import.meta.env.VITE_WEB3FORMS_KEY ?? '',
          name: clean(name),
          email: clean(email),
          message: clean(message),
          subject: `Portfolio contact from ${clean(name)}`,
          from_name: 'McJohnson Portfolio',
        }),
      });
      const data = await res.json();
      if (data.success) {
        submitted = true;
      } else {
        serverError = data.message || 'Something went wrong. Please email me directly.';
      }
    } catch {
      serverError = 'Network error. Please email me directly at hello@mcjohnson.dev';
    } finally {
      loading = false;
    }
  }
</script>

{#if submitted}
  <div class="success" role="status" aria-live="polite">
    <div class="tick">✓</div>
    <h3>Message sent!</h3>
    <p>Thanks for reaching out — I'll get back to you within 48 hours.</p>
  </div>
{:else}
  <form onsubmit={submit} novalidate aria-label="Contact form" class="form">
    <div class="row">
      <div class="field" class:err={errors.name}>
        <label for="cf-name">Name</label>
        <input id="cf-name" type="text" bind:value={name} placeholder="Your name"
          autocomplete="name" aria-describedby={errors.name ? 'err-name' : undefined} />
        {#if errors.name}<span id="err-name" class="errmsg" role="alert">{errors.name}</span>{/if}
      </div>
      <div class="field" class:err={errors.email}>
        <label for="cf-email">Email</label>
        <input id="cf-email" type="email" bind:value={email} placeholder="your@email.com"
          autocomplete="email" aria-describedby={errors.email ? 'err-email' : undefined} />
        {#if errors.email}<span id="err-email" class="errmsg" role="alert">{errors.email}</span>{/if}
      </div>
    </div>
    <div class="field" class:err={errors.message}>
      <label for="cf-msg">Message</label>
      <textarea id="cf-msg" bind:value={message} placeholder="Tell me about your project…"
        rows="5" aria-describedby={errors.message ? 'err-msg' : undefined}></textarea>
      {#if errors.message}<span id="err-msg" class="errmsg" role="alert">{errors.message}</span>{/if}
    </div>
    {#if serverError}
      <p class="server-err" role="alert">{serverError}</p>
    {/if}
    <button type="submit" disabled={loading} data-cursor="SEND" aria-label="Send message">
      {#if loading}<span class="spinner" aria-hidden="true"></span> Sending…{:else}Send message →{/if}
    </button>
  </form>
{/if}

<style>
  .form { display:flex; flex-direction:column; gap:18px; width:100%; }
  .row { display:grid; grid-template-columns:1fr 1fr; gap:18px; }
  .field { display:flex; flex-direction:column; gap:6px; }
  label { font-size:10px; font-weight:700; text-transform:uppercase; letter-spacing:0.12em; color:rgba(240,240,235,0.4); }
  input, textarea {
    background:#1a1a1a; border:1.5px solid rgba(255,255,255,0.08); border-radius:10px;
    padding:13px 16px; font-family:inherit; font-size:14px; color:#f0f0eb; outline:none; resize:none;
    transition:border-color 0.2s ease, box-shadow 0.2s ease;
  }
  input:focus, textarea:focus { border-color:#e8003a; box-shadow:0 0 0 3px rgba(232,0,58,0.1); }
  input::placeholder, textarea::placeholder { color:rgba(240,240,235,0.22); }
  .err input, .err textarea { border-color:rgba(255,100,100,0.6); }
  .errmsg { font-size:11px; color:#ff7070; font-weight:500; }
  .server-err { font-size:13px; color:#ff7070; background:rgba(255,100,100,0.08); border:1px solid rgba(255,100,100,0.2); border-radius:8px; padding:10px 14px; }
  button {
    align-self:flex-start; background:#e8003a; color:#fff; border:none; border-radius:10px;
    padding:14px 32px; font-family:inherit; font-size:13px; font-weight:700;
    text-transform:uppercase; letter-spacing:0.08em;
    transition:transform 0.25s cubic-bezier(0.34,1.56,0.64,1), background 0.2s;
    display:flex; align-items:center; gap:8px;
  }
  button:hover:not(:disabled) { transform:translateY(-3px); background:#c4002f; }
  button:disabled { opacity:0.55; }
  .spinner { width:13px; height:13px; border:2px solid rgba(255,255,255,0.25); border-top-color:#fff; border-radius:50%; animation:spin 0.7s linear infinite; }
  @keyframes spin { to { transform:rotate(360deg); } }
  .success { padding:40px 0; display:flex; flex-direction:column; gap:12px; animation:pop 0.5s cubic-bezier(0.34,1.56,0.64,1); }
  @keyframes pop { from{opacity:0;transform:scale(0.88)} to{opacity:1;transform:scale(1)} }
  .tick { width:52px; height:52px; border-radius:50%; background:rgba(40,200,80,0.12); border:2px solid rgba(40,200,80,0.35); display:flex; align-items:center; justify-content:center; font-size:20px; color:#28c840; }
  .success h3 { font-size:20px; font-weight:800; color:#f0f0eb; }
  .success p { font-size:14px; color:rgba(240,240,235,0.55); }
  @media (max-width:600px) { .row { grid-template-columns:1fr; } }
</style>
