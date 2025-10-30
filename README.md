# create-password[sait.html](https://github.com/user-attachments/files/23232336/sait.html)
<!doctype html>
<!--
Password Generator Website
Single-file HTML/CSS/JS.
Features:
- Choose length (4-128)
- Toggle: lowercase, uppercase, numbers, symbols, avoid ambiguous chars
- Real-time entropy & strength meter
- Generate, copy to clipboard, reveal/hide, download as .txt
- Accessible labels and keyboard-friendly
- No external libraries
-->
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Secure Password Creator</title>
  <style>
    :root{
      --bg:#0f1724; --card:#0b1220; --accent:#06b6d4; --muted:#9aa7b2; --success:#10b981; --danger:#ef4444;
      --glass: rgba(255,255,255,0.04);
      color-scheme: dark;
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;background:linear-gradient(180deg,#071028 0%,#08121a 100%);color:#e6f0f2}
    .wrap{max-width:980px;margin:32px auto;padding:24px}
    header{display:flex;align-items:baseline;gap:16px}
    h1{margin:0;font-size:1.6rem}
    p.lead{margin:0;color:var(--muted)}

    .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:20px;border-radius:12px;box-shadow:0 6px 30px rgba(2,6,23,0.6);margin-top:18px}

    .row{display:flex;gap:16px;flex-wrap:wrap}
    .col{flex:1;min-width:260px}

    label{display:block;font-size:0.85rem;color:var(--muted);margin-bottom:6px}
    input[type="range"]{width:100%}
    input[type="number"]{width:80px;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.06);background:transparent;color:inherit}
    .controls{display:flex;gap:12px;align-items:center}

    .switch{display:flex;align-items:center;gap:8px}
    .checkbox{display:inline-flex;align-items:center;gap:10px}
    .checkbox input{width:18px;height:18px}

    .output{display:flex;align-items:center;gap:12px;padding:14px;border-radius:10px;background:var(--glass);border:1px solid rgba(255,255,255,0.03)}
    .password{font-family:ui-monospace, SFMono-Regular, Menlo, Monaco, monospace;word-break:break-all;font-size:1.05rem}

    button{background:transparent;border:1px solid rgba(255,255,255,0.06);padding:8px 12px;border-radius:10px;cursor:pointer}
    button.primary{background:linear-gradient(90deg,var(--accent),#7c3aed);border:none;color:#042029}

    .meter{height:10px;border-radius:8px;background:rgba(255,255,255,0.04);overflow:hidden}
    .meter > i{display:block;height:100%;width:0%;background:linear-gradient(90deg,var(--success),#f59e0b)}

    .meta{display:flex;gap:12px;flex-wrap:wrap;align-items:center;margin-top:10px}
    small{color:var(--muted)}

    footer{margin-top:18px;color:var(--muted);font-size:0.85rem}

    @media (max-width:520px){.row{flex-direction:column}.controls{flex-direction:column;align-items:stretch}}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div>
        <h1>Secure Password Creator</h1>
        <p class="lead">Generate strong, random passwords with configurable options — copy or download instantly.</p>
      </div>
    </header>

    <section class="card" aria-labelledby="generator-heading">
      <h2 id="generator-heading" style="margin-top:0">Password Generator</h2>

      <div class="row" style="margin-top:12px">
        <div class="col">
          <label for="length">Length: <strong id="lenVal">16</strong></label>
          <div class="controls">
            <input id="length" type="range" min="4" max="128" value="16" aria-valuemin="4" aria-valuemax="128" />
            <input id="lengthNum" type="number" min="4" max="128" value="16" />
          </div>

          <div style="height:12px"></div>

          <div class="checkbox">
            <label><input type="checkbox" id="lower" checked /> Lowercase (a-z)</label>
          </div>
          <div class="checkbox">
            <label><input type="checkbox" id="upper" checked /> Uppercase (A-Z)</label>
          </div>
          <div class="checkbox">
            <label><input type="checkbox" id="numbers" checked /> Numbers (0-9)</label>
          </div>
          <div class="checkbox">
            <label><input type="checkbox" id="symbols" checked /> Symbols (!@#$...)</label>
          </div>
          <div class="checkbox">
            <label><input type="checkbox" id="noAmb" /> Exclude ambiguous (i, l, 1, O, 0)</label>
          </div>

          <div style="height:12px"></div>

          <div style="display:flex;gap:8px;flex-wrap:wrap">
            <button id="generate" class="primary">Generate</button>
            <button id="shuffle">Regenerate</button>
            <button id="download">Download .txt</button>
          </div>
        </div>

        <div class="col">
          <label for="output">Generated password</label>
          <div class="output" role="region" aria-live="polite">
            <div style="flex:1">
              <div id="output" class="password" tabindex="0" aria-label="generated password">&nbsp;</div>
              <small id="entropy">Entropy: — bits • Strength: —</small>
              <div style="height:8px"></div>
              <div class="meter" aria-hidden="true"><i id="meterFill"></i></div>
            </div>

            <div style="display:flex;flex-direction:column;gap:8px;align-items:center">
              <button id="copy">Copy</button>
              <button id="reveal">Show</button>
            </div>
          </div>

          <div class="meta">
            <small>Tip: use a password manager to store long random passwords securely.</small>
          </div>
        </div>
      </div>

      <footer>
        Built locally — nothing is sent to a server. The randomness uses browser crypto when available.
      </footer>
    </section>
  </div>

  <script>
    // Character sets
    const SETS = {
      lower: 'abcdefghijklmnopqrstuvwxyz',
      upper: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ',
      numbers: '0123456789',
      symbols: "!@#$%^&*()-_=+[]{};:'\\\",.<>/?`~"
    };

    // DOM
    const length = document.getElementById('length');
    const lengthNum = document.getElementById('lengthNum');
    const lenVal = document.getElementById('lenVal');
    const lower = document.getElementById('lower');
    const upper = document.getElementById('upper');
    const numbers = document.getElementById('numbers');
    const symbols = document.getElementById('symbols');
    const noAmb = document.getElementById('noAmb');
    const generateBtn = document.getElementById('generate');
    const shuffleBtn = document.getElementById('shuffle');
    const output = document.getElementById('output');
    const meterFill = document.getElementById('meterFill');
    const entropyLabel = document.getElementById('entropy');
    const copyBtn = document.getElementById('copy');
    const revealBtn = document.getElementById('reveal');
    const downloadBtn = document.getElementById('download');

    // Sync range and number
    function syncLength(v){
      length.value = v; lengthNum.value = v; lenVal.textContent = v;
    }
    length.addEventListener('input', e=> syncLength(e.target.value));
    lengthNum.addEventListener('change', e=>{
      let v = Number(e.target.value) || 16;
      if(v < 4) v = 4; if(v > 128) v = 128;
      syncLength(v);
    });

    // Remove ambiguous characters
    function filterAmbiguous(str){
      return str.replace(/[il1Lo0O]/g, '');
    }

    // Build pool
    function buildPool(){
      let pool = '';
      if(lower.checked) pool += SETS.lower;
      if(upper.checked) pool += SETS.upper;
      if(numbers.checked) pool += SETS.numbers;
      if(symbols.checked) pool += SETS.symbols;
      if(noAmb.checked) pool = filterAmbiguous(pool);
      // dedupe
      pool = Array.from(new Set(pool.split(''))).join('');
      return pool;
    }

    // Secure random integers
    function randInt(n){
      // returns integer in [0, n)
      if(window.crypto && window.crypto.getRandomValues){
        const max = Math.floor(0xFFFFFFFF / n) * n; // avoid modulo bias
        const arr = new Uint32Array(1);
        let r;
        do{ window.crypto.getRandomValues(arr); r = arr[0]; } while(r >= max);
        return r % n;
      }
      return Math.floor(Math.random()*n);
    }

    function generatePassword(){
      const pool = buildPool();
      const L = Number(length.value);
      if(pool.length === 0){ output.textContent = 'Please select at least one character set.'; updateMeter(0,0); return ''; }
      let pw = '';
      for(let i=0;i<L;i++){
        const idx = randInt(pool.length);
        pw += pool[idx];
      }
      // Show
      output.textContent = pw;
      updateEntropyAndMeter(pw, pool.length);
      return pw;
    }

    function updateEntropyAndMeter(pw, poolSize){
      if(!pw || poolSize<=0){ updateMeter(0,0); entropyLabel.textContent='Entropy: — bits • Strength: —'; return; }
      const L = pw.length;
      const entropy = Math.round(L * Math.log2(poolSize));
      // Strength: rough mapping
      let strength = 'Very weak';
      let pct = 0;
      if(entropy < 28){ strength = 'Very weak'; pct = 12; }
      else if(entropy < 36){ strength = 'Weak'; pct = 28; }
      else if(entropy < 60){ strength = 'Reasonable'; pct = 52; }
      else if(entropy < 80){ strength = 'Strong'; pct = 78; }
      else { strength = 'Very strong'; pct = 98; }

      entropyLabel.textContent = `Entropy: ${entropy} bits • Strength: ${strength}`;
      updateMeter(pct, entropy);
    }

    function updateMeter(pct, entropy){
      meterFill.style.width = pct + '%';
      // color: interpolate roughly from danger to success
      if(pct < 28) meterFill.style.background = 'linear-gradient(90deg,var(--danger),#f97316)';
      else if(pct < 52) meterFill.style.background = 'linear-gradient(90deg,#f97316,#f59e0b)';
      else if(pct < 78) meterFill.style.background = 'linear-gradient(90deg,#f59e0b,var(--accent))';
      else meterFill.style.background = 'linear-gradient(90deg,var(--success),#34d399)';
    }

    // Copy to clipboard
    copyBtn.addEventListener('click', async ()=>{
      const text = output.textContent || '';
      if(!text) return;
      try{
        await navigator.clipboard.writeText(text);
        copyBtn.textContent = 'Copied!';
        setTimeout(()=> copyBtn.textContent = 'Copy', 1400);
      }catch(e){
        // fallback
        const ta = document.createElement('textarea'); ta.value = text; document.body.appendChild(ta); ta.select();
        try{ document.execCommand('copy'); copyBtn.textContent = 'Copied!'; setTimeout(()=>copyBtn.textContent='Copy',1400);}catch(e){}
        ta.remove();
      }
    });

    // Reveal/hide
    let revealed = false;
    revealBtn.addEventListener('click', ()=>{
      revealed = !revealed;
      revealBtn.textContent = revealed ? 'Hide' : 'Show';
      output.style.webkitTextSecurity = revealed ? 'none' : 'disc';
      output.style.MozUserSelect = 'text';
    });

    // Generate handlers
    generateBtn.addEventListener('click', generatePassword);
    shuffleBtn.addEventListener('click', generatePassword);

    // Download as .txt
    downloadBtn.addEventListener('click', ()=>{
      const text = output.textContent || '';
      if(!text) return alert('No password generated yet.');
      const blob = new Blob([text], {type:'text/plain'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href = url; a.download = 'password.txt'; document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
    });

    // Auto-generate initial password on load
    window.addEventListener('DOMContentLoaded', ()=>{
      // mask output by default
      output.style.webkitTextSecurity = 'disc';
      generatePassword();
    });

    // Regenerate when options change (live feedback)
    [lower,upper,numbers,symbols,noAmb].forEach(el => el.addEventListener('change', ()=>{
      // keep existing text selected if user wants; just regenerate
      generatePassword();
    }));
    length.addEventListener('change', ()=> generatePassword());
  </script>
</body>
</html>
