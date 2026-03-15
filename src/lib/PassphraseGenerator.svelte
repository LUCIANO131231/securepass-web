<script>
  import { createEventDispatcher } from 'svelte';
  import Toggle from './Toggle.svelte';

  const dispatch = createEventDispatcher();

  const WORDS = [
    'apple','bridge','castle','dragon','eagle','forest','garden','harbor','island','jungle',
    'kernel','lemon','marble','nebula','ocean','planet','quartz','river','shadow','tiger',
    'umbrella','valley','winter','xenon','yellow','zenith','anchor','blade','canvas','drift',
    'ember','falcon','glacier','hollow','ignite','jasper','kindle','lantern','mosaic','nova',
    'onyx','prism','raven','silver','thorn','vortex','walnut','zephyr','amber','brick',
    'cobalt','dune','flare','grove','haze','ivory','jade','kite','lyric','mint',
  ];

  let wordCount    = 4;
  let separator    = '-';
  let ppCapitalize = true;
  let ppNumber     = true;
  let passphrase   = '';
  let copied       = false;

  function secureRand(max) {
    const arr = new Uint32Array(1);
    crypto.getRandomValues(arr);
    return arr[0] % max;
  }

  function generate() {
    let words = Array.from({ length: wordCount }, () => {
      let w = WORDS[secureRand(WORDS.length)];
      return ppCapitalize ? w[0].toUpperCase() + w.slice(1) : w;
    });
    if (ppNumber) {
      const n = String(secureRand(9999)).padStart(2, '0');
      words.splice(secureRand(words.length + 1), 0, n);
    }
    passphrase = words.join(separator);
    dispatch('generated', { pwd: passphrase, type: 'passphrase' });
  }

  async function copy() {
    if (!passphrase) return;
    await navigator.clipboard.writeText(passphrase);
    copied = true;
    setTimeout(() => copied = false, 1800);
  }
</script>

<!-- Output -->
<div class="card">
  <div class="pwd-display">
    <div class="pwd-text" class:generated={passphrase} class:placeholder={!passphrase}>
      {passphrase || 'Presiona generar para crear una passphrase...'}
    </div>
    <div class="pwd-actions">
      <button class="btn btn-copy" class:copied on:click={copy}>
        {copied ? '✓ Copiado!' : '📋 Copiar'}
      </button>
      <button class="btn btn-refresh" on:click={generate}>↺</button>
    </div>
  </div>
</div>

<!-- Options -->
<div class="card">
  <div class="section-title">— Opciones de Passphrase</div>

  <div class="pp-grid">
    <div class="input-group">
      <label class="input-label" for="wordCount">Número de palabras</label>
      <input id="wordCount" type="number" bind:value={wordCount} min="3" max="8"/>
    </div>
    <div class="input-group">
      <label class="input-label" for="separator">Separador</label>
      <select id="separator" bind:value={separator}>
        <option value="-">Guión  ( - )</option>
        <option value=".">Punto  ( . )</option>
        <option value="_">Guión bajo ( _ )</option>
        <option value=" ">Espacio</option>
        <option value="@">Arroba ( @ )</option>
      </select>
    </div>
  </div>

  <div class="toggles">
    <Toggle label="Capitalizar"     bind:value={ppCapitalize}/>
    <Toggle label="Incluir número"  bind:value={ppNumber}/>
  </div>

  <button class="btn btn-primary" on:click={generate}>⚡ GENERAR PASSPHRASE</button>
</div>

<style>
  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.75rem;
    margin-bottom: 1rem;
  }
  .pwd-display {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.25rem 1.5rem;
    margin-bottom: 1.25rem;
    transition: box-shadow 0.3s;
  }
  .pwd-display:hover { box-shadow: var(--glow); }
  .pwd-text {
    font-family: var(--font-mono);
    font-size: clamp(1rem, 3vw, 1.4rem);
    letter-spacing: 0.05em;
    word-break: break-all;
    line-height: 1.5;
    min-height: 2rem;
    color: var(--muted);
  }
  .pwd-text.generated   { color: var(--accent); }
  .pwd-text.placeholder { font-style: italic; font-size: 0.9rem; }
  .pwd-actions { display: flex; gap: 0.5rem; margin-top: 0.75rem; }

  .section-title {
    font-size: 0.7rem; font-family: var(--font-mono);
    letter-spacing: 0.15em; text-transform: uppercase;
    color: var(--muted); margin-bottom: 1rem;
  }
  .pp-grid  { display: grid; grid-template-columns: 1fr 1fr; gap: 0.75rem; margin-bottom: 1rem; }
  .input-group { display: flex; flex-direction: column; gap: 0.35rem; }
  .input-label { font-family: var(--font-mono); font-size: 0.68rem; letter-spacing: 0.08em; text-transform: uppercase; color: var(--muted); }
  .input-group input, .input-group select {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: 8px; padding: 0.55rem 0.75rem;
    color: var(--text); font-family: var(--font-mono);
    font-size: 0.85rem; outline: none; transition: border-color 0.2s;
  }
  .input-group input:focus, .input-group select:focus { border-color: var(--accent); }
  .toggles { display: grid; grid-template-columns: 1fr 1fr; gap: 0.5rem; margin-bottom: 1.25rem; }

  .btn {
    font-family: var(--font-mono); font-size: 0.78rem; font-weight: 700;
    letter-spacing: 0.08em; padding: 0.65rem 1.25rem;
    border-radius: 8px; border: 1px solid var(--border);
    cursor: pointer; transition: all 0.2s; text-transform: uppercase;
    display: inline-flex; align-items: center; gap: 0.4rem;
  }
  .btn-primary {
    background: var(--accent); color: #000; border-color: var(--accent);
    font-size: 0.85rem; padding: 0.8rem 1.5rem;
    width: 100%; justify-content: center;
    box-shadow: 0 4px 20px rgba(0,255,157,0.25);
  }
  .btn-primary:hover  { background: #00e68a; box-shadow: 0 4px 30px rgba(0,255,157,0.4); transform: translateY(-1px); }
  .btn-primary:active { transform: translateY(0); }
  .btn-copy    { background: var(--surface2); color: var(--text); }
  .btn-copy.copied { border-color: var(--accent); color: var(--accent); }
  .btn-refresh { background: var(--surface2); color: var(--muted); border-color: transparent; padding: 0.65rem; }
  .btn-refresh:hover { color: var(--accent); border-color: var(--accent); }
</style>