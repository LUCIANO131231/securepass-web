<script>
  export let label  = '';
  export let sample = '';
  export let value  = false;
  export let full   = false; // grid-column: 1/-1

  import { createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  function toggle() {
    value = !value;
    dispatch('change', value);
  }
</script>

<button type="button" class="toggle-item" class:on={value} class:full on:click={toggle}>
  <div>
    <div class="toggle-label">{label}</div>
    {#if sample}<div class="toggle-sample">{sample}</div>{/if}
  </div>
  <div class="switch"></div>
</button>

<style>
  .toggle-item {
    appearance: none;
    font-family: var(--font-display);
    text-align: left;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0.6rem 0.85rem;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    cursor: pointer;
    transition: border-color 0.2s;
  }
  .toggle-item:hover  { border-color: var(--accent); }
  .toggle-item.on     { border-color: rgba(0,255,157,0.3); background: rgba(0,255,157,0.04); }
  .toggle-item.full   { grid-column: 1 / -1; }
  .toggle-label       { font-size: 0.8rem; font-family: var(--font-mono); color: var(--text); }
  .toggle-sample      { font-size: 0.65rem; color: var(--muted); margin-top: 2px; }
  .switch {
    width: 32px; height: 18px;
    background: var(--border);
    border-radius: 99px;
    position: relative;
    transition: background 0.2s;
    flex-shrink: 0;
    margin-left: 0.5rem;
  }
  .switch::after {
    content: '';
    position: absolute;
    top: 3px; left: 3px;
    width: 12px; height: 12px;
    border-radius: 50%;
    background: var(--muted);
    transition: transform 0.2s, background 0.2s;
  }
  .toggle-item.on .switch        { background: rgba(0,255,157,0.3); }
  .toggle-item.on .switch::after { transform: translateX(14px); background: var(--accent); }
</style>