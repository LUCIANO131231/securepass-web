<script>
  import { createEventDispatcher } from "svelte";
  import Toggle from './Toggle.svelte';
  import StrengthMeter from './StrengthMeter.svelte';

  const dispatch = createEventDispatcher();

  let length = 16;
  let useUpper = true;
  let useLower = true;
  let useDigits = true;
  let useSymbols = false;
  let excludeAmb = false;

  let password = '';
  let copied = false;

  // crypto random
  function secureRand(max) {
    const arr = new Uint32Array(1);
    crypto.getRandomValues(arr);
    return arr[0] % max;
  }
  function pickFrom(str) { return str[secureRand(str.length)]; }

  // generate
  function generate() {
    const U = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
    const L = 'abcdefghijklmnopqrstuvwxyz';
    const D = '0123456789';
    const S = '!@#$%^&*()-_=+[]{}|;:,.<>?';

    let pool = '';
    if (useUpper)   pool += U;
    if (useLower)   pool += L;
    if (useDigits)  pool += D;
    if (useSymbols) pool += S;
    if (excludeAmb) pool = pool.replace(/[0OIl1]/g, '');
    if (!pool) return;

    let required = [];
    if (useUpper)   required.push(pickFrom(excludeAmb ? U.replace(/[OI]/g,'')  : U));
    if (useLower)   required.push(pickFrom(excludeAmb ? L.replace(/[l]/g,'')   : L));
    if (useDigits)  required.push(pickFrom(excludeAmb ? D.replace(/[01]/g,'')  : D));
    if (useSymbols) required.push(pickFrom(S));

    const rem = Array.from({ length: length - required.length }, () => pool[secureRand(pool.length)]);
    const combined = [...required, ...rem];

    for (let i = combined.length - 1; i > 0; i--) {
      const j = secureRand(i + 1);
      [combined[i], combined[j]] = [combined[j], combined[i]];
    }

    password = combined.join('');
    dispatch('generated', { pwd: password, type: 'password' });
  }

  // copy
  async function copy() {
    if (!password) return;
    await navigator.clipboard.writeText(password);
    copied = true;
    setTimeout(() => copied = false, 1800);
  }

  $: sliderPct = ((length - 6) / (64 - 6) * 100).toFixed(1);
</script>

<!-- Output -->
<div class="card">
  <div class="pwd-display">
    <div class="pwd-text" class:generated={password} class:placeholder={!password}>
      {password || 'Presiona generar para crear una contraseña...'}
    </div>
    <div class="pwd-actions">
      <button class="btn btn-copy" class:copied on:click={copy}>
        {copied ? '✓ Copiado!' : '📋 Copiar'}
      </button>
      <button class="btn btn-refresh" title="Regenerar" on:click={generate}>↺</button>
    </div>
  </div>

  <StrengthMeter {password} />
</div>

<!-- Options -->
<div class="card">
  <div class="section-title">— Configuración</div>

  <div class="slider-row">
    <div class="slider-wrap">
      <input type="range" min="6" max="64" bind:value={length}
        style="--pct:{sliderPct}%"/>
    </div>
    <div class="slider-val">{length}</div>
  </div>

  <div class="toggles">
    <Toggle label="Mayúsculas" sample="A B C D E" bind:value={useUpper}/>
    <Toggle label="Minúsculas" sample="a b c d e" bind:value={useLower}/>
    <Toggle label="Números"    sample="0 1 2 3 4" bind:value={useDigits}/>
    <Toggle label="Símbolos"   sample="! @ # $ %" bind:value={useSymbols}/>
    <Toggle label="Excluir ambiguos" sample="Elimina 0 O I l 1" bind:value={excludeAmb} full={true}/>
  </div>

  <button class="btn btn-primary" on:click={generate}>⚡ GENERAR CONTRASEÑA</button>
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
    font-size: 0.7rem;
    font-family: var(--font-mono);
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 1rem;
  }
  .slider-row  { display: flex; align-items: center; gap: 1rem; margin-bottom: 1.25rem; }
  .slider-wrap { flex: 1; }

  input[type=range] {
    -webkit-appearance: none;
    appearance: none;
    width: 100%; height: 4px;
    border-radius: 99px;
    outline: none; cursor: pointer;
    background: linear-gradient(to right, var(--accent) var(--pct,50%), var(--border) var(--pct,50%));
  }
  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 18px; height: 18px;
    border-radius: 50%;
    background: var(--accent);
    border: 3px solid var(--bg);
    box-shadow: 0 0 0 2px var(--accent);
    transition: transform 0.15s;
  }
  input[type=range]:hover::-webkit-slider-thumb { transform: scale(1.2); }

  .slider-val {
    font-family: var(--font-mono);
    font-size: 1.4rem; font-weight: 700;
    color: var(--accent);
    min-width: 2.5rem; text-align: right;
  }
  .toggles {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 0.5rem;
    margin-bottom: 1.25rem;
  }

  /* ─── Buttons ─── */
  .btn {
    font-family: var(--font-mono);
    font-size: 0.78rem; font-weight: 700;
    letter-spacing: 0.08em;
    padding: 0.65rem 1.25rem;
    border-radius: 8px;
    border: 1px solid var(--border);
    cursor: pointer;
    transition: all 0.2s;
    text-transform: uppercase;
    display: inline-flex; align-items: center; gap: 0.4rem;
  }
  .btn-primary {
    background: var(--accent); color: #000;
    border-color: var(--accent);
    font-size: 0.85rem; padding: 0.8rem 1.5rem;
    width: 100%; justify-content: center;
    box-shadow: 0 4px 20px rgba(0,255,157,0.25);
  }
  .btn-primary:hover   { background: #00e68a; box-shadow: 0 4px 30px rgba(0,255,157,0.4); transform: translateY(-1px); }
  .btn-primary:active  { transform: translateY(0); }
  .btn-copy    { background: var(--surface2); color: var(--text); }
  .btn-copy.copied { border-color: var(--accent); color: var(--accent); }
  .btn-refresh { background: var(--surface2); color: var(--muted); border-color: transparent; padding: 0.65rem; }
  .btn-refresh:hover { color: var(--accent); border-color: var(--accent); }
</style>