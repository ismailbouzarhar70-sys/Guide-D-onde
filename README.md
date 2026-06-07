<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GOR Field Simulator</title>
<link href="https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Exo+2:wght@300;400;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #070c18;
    --panel: #0d1526;
    --panel2: #111e35;
    --border: #1a2d4a;
    --accent: #00d4ff;
    --accent2: #ff6b35;
    --accent3: #39ff14;
    --text: #cde0f5;
    --muted: #4a6080;
    --ecolor: #4da6ff;
    --hcolor: #ff5555;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Exo 2', sans-serif;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
  }
  /* Header */
  header {
    background: var(--panel);
    border-bottom: 1px solid var(--border);
    padding: 12px 24px;
    display: flex;
    align-items: center;
    gap: 16px;
  }
  .logo-box {
    width: 38px; height: 38px;
    border: 1.5px solid var(--accent);
    border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    box-shadow: 0 0 14px #00d4ff44;
    flex-shrink: 0;
  }
  header h1 { font-size: 15px; font-weight: 700; letter-spacing: 3px; color: var(--text); font-family: 'Share Tech Mono', monospace; }
  header p { font-size: 11px; color: var(--muted); font-family: 'Share Tech Mono', monospace; margin-top: 2px; }
  .header-legend { margin-left: auto; font-size: 11px; color: var(--muted); font-family: 'Share Tech Mono', monospace; }
  .header-legend span { margin-left: 10px; }

  /* Layout */
  .main { display: flex; flex: 1; overflow: hidden; }
  .canvas-area { flex: 1; padding: 20px; display: flex; flex-direction: column; gap: 14px; overflow-y: auto; }
  .sidebar { width: 290px; border-left: 1px solid var(--border); background: var(--panel); padding: 18px; display: flex; flex-direction: column; gap: 16px; overflow-y: auto; }

  /* Canvas wrapper */
  .canvas-wrap {
    background: var(--panel);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 16px;
    position: relative;
  }
  canvas { display: block; width: 100%; border-radius: 6px; }

  /* Toggles */
  .toggles { display: flex; gap: 8px; flex-wrap: wrap; }
  .toggle-btn {
    padding: 6px 14px;
    border-radius: 6px;
    border: 1px solid var(--border);
    background: transparent;
    color: var(--muted);
    font-size: 12px;
    font-family: 'Share Tech Mono', monospace;
    cursor: pointer;
    transition: all 0.2s;
  }
  .toggle-btn.active-e { border-color: var(--ecolor); background: #4da6ff22; color: var(--ecolor); }
  .toggle-btn.active-h { border-color: var(--hcolor); background: #ff555522; color: var(--hcolor); }
  .toggle-btn.active-l { border-color: var(--accent3); background: #39ff1422; color: var(--accent3); }

  /* Quick modes */
  .quick-modes {
    background: var(--panel);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 14px 16px;
  }
  .quick-modes p { font-size: 11px; color: var(--muted); font-family: 'Share Tech Mono', monospace; margin-bottom: 10px; }
  .modes-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; }
  .mode-chip {
    padding: 7px 4px;
    border-radius: 6px;
    border: 1px solid var(--border);
    background: transparent;
    color: var(--muted);
    font-size: 11px;
    font-family: 'Share Tech Mono', monospace;
    cursor: pointer;
    text-align: center;
    transition: all 0.15s;
  }
  .mode-chip:hover { border-color: var(--accent); color: var(--accent); }
  .mode-chip.active { border-color: var(--accent); background: #00d4ff20; color: var(--accent); }
  .mode-chip .fc { font-size: 9px; color: var(--muted); margin-top: 2px; }
  .mode-chip.active .fc { color: var(--accent2); }

  /* Sidebar sections */
  .section-label {
    font-size: 10px;
    color: var(--muted);
    font-family: 'Share Tech Mono', monospace;
    letter-spacing: 2px;
    margin-bottom: 8px;
  }
  .mode-type-btns { display: flex; gap: 6px; }
  .mode-type-btn {
    flex: 1; padding: 8px 0;
    border-radius: 6px;
    border: 1px solid var(--border);
    background: transparent;
    color: var(--muted);
    font-size: 13px;
    font-family: 'Share Tech Mono', monospace;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
    letter-spacing: 2px;
  }
  .mode-type-btn.active { border-color: var(--accent); background: #00d4ff20; color: var(--accent); }

  /* Sliders */
  .slider-row { margin-bottom: 12px; }
  .slider-header { display: flex; justify-content: space-between; margin-bottom: 4px; }
  .slider-header span:first-child { font-size: 11px; color: var(--muted); font-family: 'Share Tech Mono', monospace; }
  .slider-header span:last-child { font-size: 11px; color: var(--accent); font-family: 'Share Tech Mono', monospace; font-weight: 600; }
  input[type=range] { width: 100%; accent-color: var(--accent); cursor: pointer; }

  /* Info card */
  .info-card {
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 14px;
    background: var(--panel2);
  }
  .mode-badge {
    display: inline-block;
    background: #00d4ff18;
    color: var(--accent);
    font-family: 'Share Tech Mono', monospace;
    font-size: 13px;
    font-weight: 700;
    padding: 3px 10px;
    border-radius: 5px;
    border: 1px solid #00d4ff44;
    margin-bottom: 12px;
  }
  .fc-label { font-size: 10px; color: var(--muted); font-family: 'Share Tech Mono', monospace; margin-bottom: 4px; }
  .fc-value { font-size: 18px; color: var(--accent2); font-family: 'Share Tech Mono', monospace; font-weight: 700; }
  .lc-value { font-size: 11px; color: var(--muted); font-family: 'Share Tech Mono', monospace; margin-top: 3px; }
  .eq-list { margin-top: 12px; }
  .eq-list .eq-label { font-size: 10px; color: var(--muted); font-family: 'Share Tech Mono', monospace; letter-spacing: 2px; margin-bottom: 6px; }
  .eq-row { display: flex; gap: 8px; margin-bottom: 4px; align-items: baseline; }
  .eq-name { font-size: 11px; font-family: 'Share Tech Mono', monospace; font-weight: 700; min-width: 26px; }
  .eq-expr { font-size: 10px; color: #94a3b8; font-family: 'Share Tech Mono', monospace; }

  /* Legend box */
  .legend-box {
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 12px 14px;
    font-size: 11px;
    color: var(--muted);
    font-family: 'Share Tech Mono', monospace;
    line-height: 1.9;
  }
  .legend-box strong { color: var(--text); font-weight: 600; display: block; margin-bottom: 4px; }

  .warn { font-size: 10px; color: #ff9800; font-family: 'Share Tech Mono', monospace; margin-top: 6px; }

  @media (max-width: 700px) {
    .main { flex-direction: column; }
    .sidebar { width: 100%; border-left: none; border-top: 1px solid var(--border); }
    .modes-grid { grid-template-columns: repeat(4, 1fr); }
  }
</style>
</head>
<body>

<header>
  <div class="logo-box">
    <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
      <rect x="1" y="5" width="18" height="10" rx="1.5" stroke="#00d4ff" stroke-width="1.5"/>
      <line x1="6" y1="5" x2="6" y2="15" stroke="#00d4ff" stroke-width="0.8" stroke-dasharray="2 1.5"/>
      <line x1="10" y1="5" x2="10" y2="15" stroke="#00d4ff" stroke-width="0.8" stroke-dasharray="2 1.5"/>
      <line x1="14" y1="5" x2="14" y2="15" stroke="#00d4ff" stroke-width="0.8" stroke-dasharray="2 1.5"/>
    </svg>
  </div>
  <div>
    <h1>GOR FIELD SIMULATOR</h1>
    <p>Guide d'ondes rectangulaire — modes TE / TM</p>
  </div>
  <div class="header-legend">
    <span style="color:#4da6ff">■</span> Champ E
    <span style="color:#ff5555">■</span> Champ H
  </div>
</header>

<div class="main">
  <!-- Canvas area -->
  <div class="canvas-area">
    <div class="canvas-wrap">
      <canvas id="cvMain" width="700" height="420"></canvas>
    </div>

    <div class="toggles">
      <button class="toggle-btn active-e" id="btnE" onclick="toggleE()">⬤ Champ E</button>
      <button class="toggle-btn active-h" id="btnH" onclick="toggleH()">⬤ Champ H</button>
      <button class="toggle-btn" id="btnL" onclick="toggleL()">⌇ Lignes de champ H</button>
    </div>

    <div class="quick-modes">
      <p>MODES STANDARDS (WR-90 · a=22.86 mm · b=10.16 mm)</p>
      <div class="modes-grid" id="modeGrid"></div>
    </div>
  </div>

  <!-- Sidebar -->
  <div class="sidebar">
    <div>
      <div class="section-label">TYPE DE MODE</div>
      <div class="mode-type-btns">
        <button class="mode-type-btn active" id="btnTE" onclick="setModeType('TE')">TE</button>
        <button class="mode-type-btn" id="btnTM" onclick="setModeType('TM')">TM</button>
      </div>
    </div>

    <div>
      <div class="section-label">INDICES DE MODE</div>
      <div class="slider-row">
        <div class="slider-header">
          <span>m (variation selon x)</span>
          <span id="valM">1</span>
        </div>
        <input type="range" id="sliderM" min="0" max="4" step="1" value="1" oninput="setM(+this.value)">
      </div>
      <div class="slider-row">
        <div class="slider-header">
          <span>n (variation selon y)</span>
          <span id="valN">0</span>
        </div>
        <input type="range" id="sliderN" min="0" max="4" step="1" value="0" oninput="setN(+this.value)">
      </div>
    </div>

    <div>
      <div class="section-label">DIMENSIONS DU GUIDE</div>
      <div class="slider-row">
        <div class="slider-header">
          <span>a (grand côté)</span>
          <span id="valA">22.9 mm</span>
        </div>
        <input type="range" id="sliderA" min="5" max="80" step="0.1" value="22.86" oninput="setA(+this.value)">
      </div>
      <div class="slider-row">
        <div class="slider-header">
          <span>b (petit côté)</span>
          <span id="valB">10.2 mm</span>
        </div>
        <input type="range" id="sliderB" min="2" max="40" step="0.1" value="10.16" oninput="setB(+this.value)">
      </div>
      <div id="ratioInfo" style="font-size:11px;color:var(--muted);font-family:'Share Tech Mono',monospace;"></div>
    </div>

    <div class="info-card">
      <div class="mode-badge" id="modeBadge">TE_10</div>
      <div id="warnTM" class="warn" style="display:none">⚠ TM_m0 et TM_0n non physiques</div>
      <div class="fc-label">FRÉQUENCE DE COUPURE</div>
      <div class="fc-value" id="fcVal">—</div>
      <div class="lc-value" id="lcVal">—</div>
      <div class="eq-list">
        <div class="eq-label">COMPOSANTES DE CHAMP</div>
        <div id="eqList"></div>
      </div>
    </div>

    <div class="legend-box">
      <strong>LÉGENDE</strong>
      <div><span style="color:#4da6ff">→</span> Champ électrique E</div>
      <div><span style="color:#ff5555">→</span> Champ magnétique H</div>
      <div style="margin-top:6px">Animation : variation temporelle cos(ωt) et sin(ωt) des champs.</div>
    </div>
  </div>
</div>

<script>
// ── State ────────────────────────────────────────────────────────────────────
let state = {
  modeType: 'TE', m: 1, n: 0,
  a: 22.86, b: 10.16,
  showE: true, showH: true, showLines: false,
  phase: 0
};

// ── Quick modes ───────────────────────────────────────────────────────────────
const QUICK_MODES = [
  ['TE',1,0],['TE',2,0],['TE',0,1],['TE',1,1],
  ['TM',1,1],['TM',2,1],['TE',3,0],['TM',1,2]
];

function formatFreq(f) {
  if (f >= 1e9) return (f/1e9).toFixed(2)+' GHz';
  if (f >= 1e6) return (f/1e6).toFixed(2)+' MHz';
  return (f/1e3).toFixed(2)+' kHz';
}
function fc(m,n,a,b) {
  return (3e8/2)*Math.sqrt((m/a)**2+(n/b)**2);
}

function buildModeGrid() {
  const grid = document.getElementById('modeGrid');
  grid.innerHTML = '';
  QUICK_MODES.forEach(([t,mi,ni]) => {
    const f = fc(mi,ni,0.02286,0.01016);
    const btn = document.createElement('button');
    btn.className = 'mode-chip';
    btn.id = `chip_${t}${mi}${ni}`;
    btn.innerHTML = `<div>${t}<sub>${mi}${ni}</sub></div><div class="fc">${formatFreq(f)}</div>`;
    btn.onclick = () => { applyMode(t,mi,ni); };
    grid.appendChild(btn);
  });
  refreshChips();
}

function applyMode(t,mi,ni) {
  state.modeType = t;
  state.m = mi; state.n = ni;
  document.getElementById('btnTE').classList.toggle('active', t==='TE');
  document.getElementById('btnTM').classList.toggle('active', t==='TM');
  document.getElementById('sliderM').value = mi;
  document.getElementById('sliderN').value = ni;
  updateSliderLimits();
  updateInfo();
  refreshChips();
}

function refreshChips() {
  QUICK_MODES.forEach(([t,mi,ni]) => {
    const el = document.getElementById(`chip_${t}${mi}${ni}`);
    if (el) el.classList.toggle('active', state.modeType===t && state.m===mi && state.n===ni);
  });
}

// ── Controls ──────────────────────────────────────────────────────────────────
function setModeType(t) {
  state.modeType = t;
  document.getElementById('btnTE').classList.toggle('active', t==='TE');
  document.getElementById('btnTM').classList.toggle('active', t==='TM');
  if (t==='TM') {
    if (state.m===0) { state.m=1; document.getElementById('sliderM').value=1; }
    if (state.n===0) { state.n=1; document.getElementById('sliderN').value=1; }
  }
  updateSliderLimits();
  updateInfo(); refreshChips();
}
function updateSliderLimits() {
  const minVal = state.modeType==='TM' ? 1 : 0;
  document.getElementById('sliderM').min = minVal;
  document.getElementById('sliderN').min = minVal;
}
function setM(v) {
  state.m = v; document.getElementById('valM').textContent = v;
  updateInfo(); refreshChips();
}
function setN(v) {
  state.n = v; document.getElementById('valN').textContent = v;
  updateInfo(); refreshChips();
}
function setA(v) {
  state.a = v; document.getElementById('valA').textContent = v.toFixed(1)+' mm';
  updateRatio(); updateInfo();
}
function setB(v) {
  state.b = v; document.getElementById('valB').textContent = v.toFixed(1)+' mm';
  updateRatio(); updateInfo();
}
function updateRatio() {
  const r = state.a/state.b;
  const ok = Math.abs(r-2)<0.05;
  document.getElementById('ratioInfo').innerHTML =
    `Rapport a/b = <span style="color:var(--accent)">${r.toFixed(2)}</span>` +
    (ok ? ` <span style="color:var(--accent3)">✓ standard</span>` : '');
}
function toggleE() {
  state.showE = !state.showE;
  document.getElementById('btnE').className = 'toggle-btn' + (state.showE?' active-e':'');
}
function toggleH() {
  state.showH = !state.showH;
  document.getElementById('btnH').className = 'toggle-btn' + (state.showH?' active-h':'');
}
function toggleL() {
  state.showLines = !state.showLines;
  document.getElementById('btnL').className = 'toggle-btn' + (state.showLines?' active-l':'');
}

// ── Info panel ────────────────────────────────────────────────────────────────
function updateInfo() {
  const {modeType,m,n,a,b} = state;
  const f = fc(m,n,a/1000,b/1000);
  document.getElementById('modeBadge').textContent = `${modeType}_${m}${n}`;
  document.getElementById('fcVal').textContent = formatFreq(f);
  document.getElementById('lcVal').textContent = `λ_c = ${(3e8/f*100).toFixed(1)} cm`;

  const tmWarn = modeType==='TM' && (m===0||n===0);
  document.getElementById('warnTM').style.display = tmWarn ? 'block' : 'none';

  const eqs = modeType==='TE' ? [
    ['E_y', `E₀·sin(${m}πx/a)·cos(${n}πy/b)`, '#4da6ff'],
    ['E_x', `-E₀·cos(${m}πx/a)·sin(${n}πy/b)`, '#4da6ff'],
    ['H_z', `H₀·cos(${m}πx/a)·cos(${n}πy/b)`, '#ff5555'],
  ] : [
    ['E_z', `E₀·sin(${m}πx/a)·sin(${n}πy/b)`, '#4da6ff'],
    ['H_x', `-H₀·sin(${m}πx/a)·cos(${n}πy/b)`, '#ff5555'],
    ['H_y', `H₀·cos(${m}πx/a)·sin(${n}πy/b)`, '#ff5555'],
  ];
  document.getElementById('eqList').innerHTML = eqs.map(([name,expr,col]) =>
    `<div class="eq-row"><span class="eq-name" style="color:${col}">${name}</span><span class="eq-expr">= ${expr}</span></div>`
  ).join('');
}

// ── Field math ────────────────────────────────────────────────────────────────
function fields(modeType, m, n, nx, ny) {
  const kx = m*Math.PI, ky = n*Math.PI;
  const sX=Math.sin(kx*nx), cX=Math.cos(kx*nx);
  const sY=Math.sin(ky*ny), cY=Math.cos(ky*ny);
  const kc2 = kx*kx + ky*ky || 1;
  let Ex,Ey,Hx,Hy;
  if (modeType==='TE') {
    Ex = -(ky/kc2)*cX*sY;
    Ey =  (kx/kc2)*sX*cY;
    Hx = -(kx*ky/kc2)*sX*sY;
    Hy =  ((kc2-ky*ky)/kc2)*cX*cY;
  } else {
    Ex = (kx/kc2)*cX*sY;
    Ey = (ky/kc2)*sX*cY;
    Hx = -(ky/kc2)*sX*cY;
    Hy =  (kx/kc2)*cX*sY;
  }
  return {Ex,Ey,Hx,Hy};
}

// ── Canvas rendering ──────────────────────────────────────────────────────────
const canvas = document.getElementById('cvMain');
const ctx = canvas.getContext('2d');

function arrow(ctx, cx, cy, dx, dy, color, alpha) {
  if (alpha < 0.04) return;
  ctx.strokeStyle = color.replace(')',`,${alpha.toFixed(2)})`).replace('rgb','rgba');
  ctx.lineWidth = 1.3;
  ctx.beginPath();
  ctx.moveTo(cx-dx*.5, cy-dy*.5);
  ctx.lineTo(cx+dx*.5, cy+dy*.5);
  ctx.stroke();
  const angle = Math.atan2(dy,dx), al=4;
  ctx.beginPath();
  ctx.moveTo(cx+dx*.5, cy+dy*.5);
  ctx.lineTo(cx+dx*.5-al*Math.cos(angle-.5), cy+dy*.5-al*Math.sin(angle-.5));
  ctx.moveTo(cx+dx*.5, cy+dy*.5);
  ctx.lineTo(cx+dx*.5-al*Math.cos(angle+.5), cy+dy*.5-al*Math.sin(angle+.5));
  ctx.stroke();
}

function render() {
  const W=canvas.width, H=canvas.height;
  const pad=36, gw=W-2*pad, gh=H-2*pad;
  const {modeType,m,n,phase,showE,showH,showLines} = state;

  ctx.clearRect(0,0,W,H);

  // Grid background
  ctx.strokeStyle='#111e35'; ctx.lineWidth=0.5;
  for(let i=0;i<=10;i++){
    ctx.beginPath(); ctx.moveTo(pad+i/10*gw,pad); ctx.lineTo(pad+i/10*gw,pad+gh); ctx.stroke();
    ctx.beginPath(); ctx.moveTo(pad,pad+i/10*gh); ctx.lineTo(pad+gw,pad+i/10*gh); ctx.stroke();
  }

  const toX = nx => pad+nx*gw;
  const toY = ny => pad+(1-ny)*gh;
  const steps = 20;

  // ── E field arrows ────────────────────────────────────────────────────────
  if (showE) {
    for(let i=1;i<steps;i++) for(let j=1;j<steps;j++){
      const nx=i/steps, ny=j/steps;
      const {Ex,Ey} = fields(modeType,m,n,nx,ny);
      const mag = Math.sqrt(Ex*Ex+Ey*Ey);
      if(mag<0.01) continue;
      const amp = Math.cos(phase)*mag;
      const scale = 13*Math.abs(amp)/(mag+.001);
      const sign = Math.sign(amp)||1;
      const dx=(Ex/mag)*scale*sign, dy=-(Ey/mag)*scale*sign;
      const alpha = Math.min(1,mag*2.5)*Math.abs(Math.cos(phase));
      arrow(ctx, toX(nx),toY(ny),dx,dy,'rgb(77,166,255)',alpha);
    }
  }

  // ── H field ───────────────────────────────────────────────────────────────
  if (showH) {
    if (!showLines) {
      for(let i=1;i<steps;i++) for(let j=1;j<steps;j++){
        const nx=i/steps, ny=j/steps;
        const {Hx,Hy} = fields(modeType,m,n,nx,ny);
        const mag = Math.sqrt(Hx*Hx+Hy*Hy);
        if(mag<0.01) continue;
        const amp = Math.sin(phase)*mag;
        const scale = 11*Math.abs(amp)/(mag+.001);
        const sign = Math.sign(amp)||1;
        const dx=(Hx/mag)*scale*sign, dy=-(Hy/mag)*scale*sign;
        const alpha = Math.min(1,mag*2.5)*Math.abs(Math.sin(phase));
        arrow(ctx, toX(nx),toY(ny),dx,dy,'rgb(255,85,85)',alpha);
      }
    } else {
      const numL = Math.max(m,n)*3+5;
      ctx.lineWidth=1.3;
      for(let li=0;li<numL;li++){
        let px=(li+.5)/numL, py=0.02;
        ctx.beginPath(); ctx.strokeStyle='rgba(255,100,100,0.65)';
        let first=true;
        for(let s=0;s<350;s++){
          const {Hx,Hy}=fields(modeType,m,n,px,py);
          const mag=Math.sqrt(Hx*Hx+Hy*Hy)+1e-9;
          const dt=0.005;
          px+=Hx/mag*dt; py+=Hy/mag*dt;
          if(px<0||px>1||py<0||py>1) break;
          if(first){ctx.moveTo(toX(px),toY(py));first=false;}
          else ctx.lineTo(toX(px),toY(py));
        }
        ctx.stroke();
      }
    }
  }

  // ── Walls ─────────────────────────────────────────────────────────────────
  ctx.save();
  ctx.strokeStyle='#00d4ff'; ctx.lineWidth=2.5;
  ctx.shadowColor='#00d4ff'; ctx.shadowBlur=8;
  ctx.strokeRect(pad,pad,gw,gh);
  ctx.restore();

  // ── Axis labels ───────────────────────────────────────────────────────────
  ctx.fillStyle='#4a6080'; ctx.font="12px 'Share Tech Mono',monospace";
  ctx.fillText('x', W-pad+4, H-pad+4);
  ctx.fillText('y', pad-20, pad-6);
  ctx.fillStyle='#00d4ff88'; ctx.font="bold 12px 'Share Tech Mono',monospace";
  ctx.fillText('a', pad+gw/2-5, H-4);
  ctx.save(); ctx.translate(12,pad+gh/2+5); ctx.rotate(-Math.PI/2);
  ctx.fillText('b', 0, 0); ctx.restore();

  // ── Mode label overlay ────────────────────────────────────────────────────
  ctx.fillStyle='#00d4ff33';
  ctx.fillRect(pad+6, pad+6, 70, 20);
  ctx.fillStyle='#00d4ff'; ctx.font="bold 12px 'Share Tech Mono',monospace";
  ctx.fillText(`${modeType}${m}${n}`, pad+12, pad+19);

  state.phase += 0.04;
  requestAnimationFrame(render);
}

// ── Init ──────────────────────────────────────────────────────────────────────
buildModeGrid();
updateInfo();
updateRatio();
render();
</script>
</body>
</html>
