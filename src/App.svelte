<script>
  import { onMount } from 'svelte';
  import PasswordGenerator  from './lib/PasswordGenerator.svelte';
  import PassphraseGenerator from './lib/PassphraseGenerator.svelte';
  import History            from './lib/History.svelte';

  // ─── State global ─────────────────────────────────────────
  let activeTab = 'password';
  let darkMode  = true;
  let history   = [];

  // ─── History (localStorage) ───────────────────────────────
  function onGenerated(e) {
    const { pwd, type } = e.detail;
    history = [
      { pwd, type, time: new Date().toLocaleTimeString() },
      ...history
    ].slice(0, 30);
    localStorage.setItem('securepass_history', JSON.stringify(history));
  }

  function clearHistory() {
    history = [];
    localStorage.removeItem('securepass_history');
  }

  // ─── Theme ────────────────────────────────────────────────
  function toggleTheme() {
    darkMode = !darkMode;
    const theme = darkMode ? 'dark' : 'light';
    document.documentElement.setAttribute('data-theme', theme);
    localStorage.setItem('securepass_theme', theme);
  }

  // ─── Init ─────────────────────────────────────────────────
  onMount(() => {
    const savedHistory = localStorage.getItem('securepass_history');
    if (savedHistory) history = JSON.parse(savedHistory);

    const savedTheme = localStorage.getItem('securepass_theme') || 'dark';
    darkMode = savedTheme === 'dark';
    document.documentElement.setAttribute('data-theme', savedTheme);
  });
</script>

<div class="container">

  <!-- Header -->
  <header>
    <div class="logo">
      <div class="logo-icon">🔐</div>
      <div>
        <h1>Secure<span class="accent">Pass</span></h1>
        <small>Generador seguro v1.0</small>
      </div>
    </div>
    <button class="theme-btn" on:click={toggleTheme}>
      {darkMode ? '☀ LIGHT' : '🌙 DARK'}
    </button>
  </header>

  <!-- Tabs -->
  <div class="tabs">
    <button class="tab" class:active={activeTab === 'password'}   on:click={() => activeTab = 'password'}>
      // Contraseña
    </button>
    <button class="tab" class:active={activeTab === 'passphrase'} on:click={() => activeTab = 'passphrase'}>
      // Passphrase
    </button>
    <button class="tab" class:active={activeTab === 'history'}    on:click={() => activeTab = 'history'}>
      // Historial ({history.length})
    </button>
  </div>

  <!-- Views -->
  {#if activeTab === 'password'}
    <div style="animation: fadeUp 0.25s ease; width:100%">
      <PasswordGenerator on:generated={onGenerated}/>
    </div>
  {/if}

  {#if activeTab === 'passphrase'}
    <div style="animation: fadeUp 0.25s ease; width:100%">
      <PassphraseGenerator on:generated={onGenerated}/>
    </div>
  {/if}

  {#if activeTab === 'history'}
    <div style="animation: fadeUp 0.25s ease; width:100%">
      <History {history} on:clear={clearHistory}/>
    </div>
  {/if}

  <footer>
    Stack: <span class="accent">Svelte</span> + <span class="accent">Vite</span> + <span class="accent">Tailwind</span>
    <br/>Todas las contraseñas se generan localmente — nunca salen de tu navegador
  </footer>

</div>

<style>
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(8px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  .container {
    width: 100%; max-width: 760px;
    margin: 0 auto;
    padding: 2rem 1rem 4rem;
  }

  /* Header */
  header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 2.5rem; }
  .logo  { display: flex; align-items: center; gap: 0.75rem; }
  .logo-icon {
    width: 40px; height: 40px;
    background: linear-gradient(135deg, var(--accent), var(--accent2));
    border-radius: 10px;
    display: flex; align-items: center; justify-content: center;
    font-size: 1.2rem; box-shadow: var(--glow);
  }
  h1        { font-size: 1.1rem; font-weight: 800; letter-spacing: -0.02em; line-height: 1; }
  .accent   { color: var(--accent); }
  small     { display: block; font-size: 0.65rem; font-family: var(--font-mono); color: var(--muted); letter-spacing: 0.1em; text-transform: uppercase; margin-top: 2px; }
  .theme-btn {
    background: var(--surface); border: 1px solid var(--border);
    color: var(--text); padding: 0.5rem 1rem; border-radius: 8px;
    cursor: pointer; font-family: var(--font-mono); font-size: 0.75rem;
    letter-spacing: 0.05em; transition: all 0.2s;
  }
  .theme-btn:hover { border-color: var(--accent); color: var(--accent); }

  /* Tabs */
  .tabs {
    display: flex; gap: 0.25rem;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 0.25rem; margin-bottom: 1.5rem;
  }
  .tab {
    flex: 1; padding: 0.6rem; text-align: center;
    border-radius: 8px; cursor: pointer;
    font-size: 0.8rem; font-family: var(--font-mono);
    font-weight: 700; letter-spacing: 0.05em;
    color: var(--muted); transition: all 0.2s;
    border: none; background: transparent;
  }
  .tab.active { background: var(--surface2); color: var(--accent); box-shadow: inset 0 0 0 1px rgba(0,255,157,0.2); }

  /* Footer */
  footer {
    text-align: center; margin-top: 2rem;
    font-family: var(--font-mono); font-size: 0.65rem;
    color: var(--muted); letter-spacing: 0.08em;
  }
</style>