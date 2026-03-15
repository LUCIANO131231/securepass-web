<script>
  export let history = [];

  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  async function copy(pwd) {
    await navigator.clipboard.writeText(pwd);
  }

  function clear() {
    dispatch('clear');
  }

  function badgeClass(item) {
    if (item.type === 'passphrase') return 'badge-strong';
    return item.pwd.length >= 16 ? 'badge-strong' : item.pwd.length >= 10 ? 'badge-medium' : 'badge-weak';
  }

  function badgeLabel(item) {
    if (item.type === 'passphrase') return 'PHRASE';
    return item.pwd.length >= 16 ? 'Fuerte' : 'Media';
  }
</script>

<div class="card">
  <div class="history-header">
    <div class="section-title">— Historial de sesión</div>
    <button class="btn-ghost" on:click={clear}>Limpiar</button>
  </div>

  <div class="history-list">
    {#if history.length === 0}
      <div class="empty-state">📭 No hay contraseñas generadas aún</div>
    {:else}
      {#each history as item (item.time + item.pwd)}
        <div class="history-item">
          <div class="history-pwd">{item.pwd}</div>
          <span class="history-badge {badgeClass(item)}">{badgeLabel(item)}</span>
          <button class="btn-mini" on:click={() => copy(item.pwd)}>Copiar</button>
        </div>
      {/each}
    {/if}
  </div>
</div>

<style>
  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.75rem;
  }
  .history-header {
    display: flex; justify-content: space-between;
    align-items: center; margin-bottom: 0.75rem;
  }
  .section-title {
    font-size: 0.7rem; font-family: var(--font-mono);
    letter-spacing: 0.15em; text-transform: uppercase; color: var(--muted);
  }
  .btn-ghost {
    background: transparent; border: 1px solid var(--border);
    color: var(--muted); padding: 0.3rem 0.75rem;
    border-radius: 8px; cursor: pointer;
    font-family: var(--font-mono); font-size: 0.7rem;
    transition: all 0.2s;
  }
  .btn-ghost:hover { color: var(--text); border-color: var(--text); }

  .history-list { display: flex; flex-direction: column; gap: 0.4rem; }

  .history-item {
    display: flex; align-items: center; justify-content: space-between;
    padding: 0.7rem 1rem;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    animation: fadeUp 0.2s ease;
  }
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(4px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  .history-pwd {
    font-family: var(--font-mono); font-size: 0.8rem;
    color: var(--text); word-break: break-all;
    flex: 1; margin-right: 0.75rem;
  }
  .history-badge {
    font-family: var(--font-mono); font-size: 0.6rem;
    padding: 2px 7px; border-radius: 99px;
    margin-right: 0.5rem; flex-shrink: 0;
  }
  .badge-strong { background: rgba(0,255,157,0.12); color: var(--accent); }
  .badge-medium { background: rgba(255,211,42,0.12); color: #ffd32a; }
  .badge-weak   { background: rgba(255,107,107,0.12); color: #ff6b6b; }

  .btn-mini {
    padding: 0.3rem 0.55rem; font-size: 0.65rem;
    border-radius: 5px; background: var(--surface);
    border: 1px solid var(--border); color: var(--muted);
    cursor: pointer; font-family: var(--font-mono);
    transition: all 0.15s; flex-shrink: 0;
  }
  .btn-mini:hover { color: var(--accent); border-color: var(--accent); }

  .empty-state {
    text-align: center; padding: 2rem;
    color: var(--muted); font-family: var(--font-mono); font-size: 0.8rem;
  }
</style>