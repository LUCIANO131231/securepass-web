<script>
  export let password = '';

  $: checks = {
    length12: password.length >= 12,
    upper:    /[A-Z]/.test(password),
    lower:    /[a-z]/.test(password),
    digit:    /[0-9]/.test(password),
    symbol:   /[^A-Za-z0-9]/.test(password),
    noRepeat: !(/(.)\1{2,}/.test(password)),
  };

  $: score = (() => {
    if (!password) return 0;
    let s = 0;
    s += checks.length12        ? 20 : 0;
    s += password.length >= 20  ? 10 : 0;
    s += checks.upper           ? 15 : 0;
    s += checks.lower           ? 15 : 0;
    s += checks.digit           ? 15 : 0;
    s += checks.symbol          ? 20 : 0;
    s += checks.noRepeat        ?  5 : 0;
    return s;
  })();

  $: pool = (checks.upper?26:0)+(checks.lower?26:0)+(checks.digit?10:0)+(checks.symbol?32:0)||1;
  $: entropy = password ? Math.round(password.length * Math.log2(pool)) : 0;

  $: activeSegs = score >= 90 ? 5 : score >= 70 ? 4 : score >= 50 ? 3 : score >= 30 ? 2 : password ? 1 : 0;

  $: strengthLabel = !password ? '— Genera una contraseña' :
    score >= 85 ? 'Muy fuerte' :
    score >= 65 ? 'Fuerte'     :
    score >= 45 ? 'Media'      :
    score >= 25 ? 'Débil'      : 'Muy débil';

  $: labelClass = score >= 85 ? 's5' : score >= 65 ? 's4' : score >= 45 ? 's3' : score >= 25 ? 's2' : 's1';
</script>

<!-- Strength bar -->
<div class="strength-bar-wrap">
  {#each [1,2,3,4,5] as seg}
    <div class="strength-seg"
      class:active={seg <= activeSegs}
      data-active={activeSegs}>
    </div>
  {/each}
</div>

<div class="strength-meta">
  <span class="strength-label {password ? labelClass : ''}">{strengthLabel}</span>
  <span>~{entropy} bits de entropía</span>
</div>

<!-- Checks -->
<div class="checks-grid">
  <div class="check-item" class:ok={checks.length12}><div class="dot"></div>Longitud ≥ 12</div>
  <div class="check-item" class:ok={checks.upper}>   <div class="dot"></div>Mayúsculas</div>
  <div class="check-item" class:ok={checks.lower}>   <div class="dot"></div>Minúsculas</div>
  <div class="check-item" class:ok={checks.digit}>   <div class="dot"></div>Números</div>
  <div class="check-item" class:ok={checks.symbol}>  <div class="dot"></div>Símbolos</div>
  <div class="check-item" class:ok={checks.noRepeat}><div class="dot"></div>Sin repetición</div>
</div>

<style>
  .strength-bar-wrap { display: flex; gap: 4px; margin-bottom: 0.4rem; }

  .strength-seg {
    height: 4px; flex: 1;
    border-radius: 99px;
    background: var(--border);
    transition: background 0.4s, box-shadow 0.4s;
  }
  .strength-seg.active[data-active="1"] { background: #ff6b6b; }
  .strength-seg.active[data-active="2"] { background: #ff9f43; }
  .strength-seg.active[data-active="3"] { background: #ffd32a; }
  .strength-seg.active[data-active="4"] { background: #26de81; box-shadow: 0 0 6px #26de8180; }
  .strength-seg.active[data-active="5"] { background: var(--accent); box-shadow: 0 0 8px var(--accent); }

  .strength-meta {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-family: var(--font-mono);
    font-size: 0.72rem;
    color: var(--muted);
    margin-bottom: 1rem;
  }
  .strength-label    { font-weight: 700; }
  .strength-label.s1 { color: #ff6b6b; }
  .strength-label.s2 { color: #ff9f43; }
  .strength-label.s3 { color: #ffd32a; }
  .strength-label.s4 { color: #26de81; }
  .strength-label.s5 { color: var(--accent); }

  .checks-grid  { display: grid; grid-template-columns: 1fr 1fr; gap: 0.35rem; margin-top: 0.75rem; }
  .check-item   { font-family: var(--font-mono); font-size: 0.68rem; color: var(--muted); display: flex; align-items: center; gap: 0.4rem; }
  .check-item.ok { color: var(--text); }
  .dot          { width: 6px; height: 6px; border-radius: 50%; background: var(--border); flex-shrink: 0; transition: background 0.3s, box-shadow 0.3s; }
  .check-item.ok .dot { background: var(--accent); box-shadow: 0 0 4px var(--accent); }
</style>