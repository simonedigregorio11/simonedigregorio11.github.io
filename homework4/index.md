---
title: "Homework 4"
layout: default
permalink: /homework4/
---

[← Back to Home](/)

---

<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>Bernoulli</title>
  <meta name="viewport" content="width=device-width,initial-scale=1" />
</head>
<body>
  <div class="bernoulli-container wrap">
    <div class="controls">
      <div class="ctrl"><label for="N">Trials</label><input id="N" type="number" min="1" max="40000" value="200"></div>
      <div class="ctrl"><label for="paths">Paths</label><input id="paths" type="number" min="1" max="100000" value="200"></div>
      <div class="ctrl"><label for="p">p</label><input id="p" type="number" min="0" max="1" step="0.01" value="0.5"></div>
      <div class="ctrl"><label for="delay">Batch delay (ms)</label><input id="delay" type="number" min="0" max="2000" value="20"></div>
    </div>
    <div class="buttons">
      <button id="go">Run</button>
      <button class="danger" id="wipe">Reset</button>
    </div>
    <br>
    <div id="stage" class="board"></div>
    <div class="legend">
      <div><span class="sw" style="background:var(--line)"></span> trajectories</div>
      <div><span class="sw" style="background:var(--mean)"></span> mean line (t·p)</div>
      <div><span class="sw" style="background:var(--hist)"></span> histogram (final successes)</div>
      <div><span class="sw" style="background:var(--gauss)"></span> Gaussian overlay</div>
    </div>
    <br>
    <div id="note" class="meta">Ready. Set params and click Run.</div>
    <!-- Progress bar -->
    <div id="progress" class="progress" aria-live="polite" aria-hidden="true">
      <div class="progress__track">
        <div class="progress__bar" style="width:0%"></div>
      </div>
      <div class="progress__label">0%</div>
    </div>
  </div>

<script>
    
/* Bernoulli Trajectories */

const ui = {
  N: document.getElementById('N'),
  P: document.getElementById('p'),
  K: document.getElementById('paths'),
  delay: document.getElementById('delay'),
  go: document.getElementById('go'),
  wipe: document.getElementById('wipe'),
  stage: document.getElementById('stage'),
  note: document.getElementById('note'),
  progress: document.getElementById('progress'),
  progressBar: document.querySelector('#progress .progress__bar'),
  progressLabel: document.querySelector('#progress .progress__label')
};

// ---------- Canvas setup ----------
let canvas, ctx, dims;
function ensureCanvas(){
  if (!canvas){
    canvas = document.createElement('canvas');
    ui.stage.innerHTML = '';
    ui.stage.appendChild(canvas);
    ctx = canvas.getContext('2d', { alpha:true, desynchronized:true });
    window.addEventListener('resize', onResize);
  }
}
function onResize(){
  resizeCanvas();
  drawFrame(int(ui.N.value, 200), dims);
}
function resizeCanvas(){
  const dpr = Math.max(1, Math.floor(window.devicePixelRatio || 1));
  const cssW = ui.stage.clientWidth || 900;
  const cssH = ui.stage.clientHeight || 460;
  canvas.width = Math.floor(cssW * dpr);
  canvas.height = Math.floor(cssH * dpr);
  canvas.style.width = cssW + 'px';
  canvas.style.height = cssH + 'px';
  ctx.setTransform(dpr,0,0,dpr,0,0);
  dims = makeDims(cssW, cssH);
}

// ---------- geometry & style ----------
function makeDims(W,H){
  const M = {l:78, r:44, t:28, b:58};
  return {W,H,M,plotW: W - M.l - M.r, plotH: H - M.t - M.b};
}
const colors = {
  grid: getCssVar('--stroke'),
  axes: '#FFFFFF',
  txt: '#FFFFFF',
  line: getCssVar('--line'),
  lineFaint: hexWithAlpha(getCssVar('--line'), 0.14),
  mean: getCssVar('--mean'),
  hist: getCssVar('--hist'),
  gauss: getCssVar('--gauss')
};
function getCssVar(name){
  const v = getComputedStyle(document.documentElement).getPropertyValue(name).trim();
  return v || '#ffffff';
}
function hexWithAlpha(hex, alpha){
  const m = /^#([0-9a-f]{6})$/i.exec(hex);
  if (!m){ return `rgba(255,255,255,${alpha})`; }
  const r = parseInt(hex.slice(1,3),16);
  const g = parseInt(hex.slice(3,5),16);
  const b = parseInt(hex.slice(5,7),16);
  return `rgba(${r},${g},${b},${alpha})`;
}

// ---------- mappers ----------
function makeMaps(N,maxY,d){
  const {M,plotW,plotH} = d;
  const xmap = t => M.l + plotW * (t / N);
  const ymap = y => M.t + plotH * (1 - y / maxY);
  return {xmap, ymap, baseY: M.t + plotH, M, plotW, plotH};
}

// ---------- primitives ----------
function line(x1,y1,x2,y2, color, width=1){
  ctx.beginPath();
  ctx.moveTo(x1,y1); ctx.lineTo(x2,y2);
  ctx.lineWidth = width; ctx.strokeStyle = color; ctx.lineCap = 'round'; ctx.stroke();
}
function polyline(points, color, width=1, alpha=1){
  ctx.save(); ctx.globalAlpha = alpha; ctx.beginPath();
  ctx.moveTo(points[0][0], points[0][1]);
  for (let i=1;i<points.length;i++) ctx.lineTo(points[i][0], points[i][1]);
  ctx.lineWidth = width; ctx.strokeStyle = color; ctx.lineJoin = 'round'; ctx.lineCap = 'round'; ctx.stroke(); ctx.restore();
}
function text(x,y, str, color, size=12, align='start'){
  ctx.fillStyle = color; ctx.font = `${size}px ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto`;
  ctx.textAlign = align; ctx.textBaseline = 'alphabetic'; ctx.fillText(str, x, y);
}

// ---------- frame ----------
function drawFrame(N, d){
  const {M,W,H,plotH,plotW} = d;
  ctx.clearRect(0,0,W,H);

  // grid
  for (let i=0;i<=5;i++){
    const y = M.t + plotH * (i/5);
    line(M.l,y, W-M.r,y, colors.grid, 1);
  }
  // axes
  line(M.l, M.t, M.l, H-M.b, colors.axes, 1.2);
  line(M.l, H-M.b, W-M.r, H-M.b, colors.axes, 1.2);

  // axis labels (adjusted to avoid overlap)
  // x-label centered and pushed further down
  const xMid = M.l + plotW/2;
  text(xMid, H - M.b + 36, 'tries →', colors.txt, 13, 'center');

  // y-label vertical, left of tick numbers
  ctx.save();
  ctx.translate(M.l - 58, M.t + plotH/2);
  ctx.rotate(-Math.PI/2);
  text(0, 0, 'successes →', colors.txt, 13, 'center');
  ctx.restore();

  // y ticks & numbers
  for (let i=0;i<=5;i++){
    const val = Math.round(N * (1 - i/5));
    const y = M.t + plotH * (i/5);
    text(M.l - 34, y + 4, String(val), colors.txt, 12, 'end');
  }
  // x ticks & numbers
  const xs = [0, Math.floor(N/4), Math.floor(N/2), Math.floor(3*N/4), N];
  for (const v of xs){
    const xx = M.l + plotW * (v / N);
    text(xx, H - M.b + 20, String(v), colors.txt, 12, 'center');
  }
}

// ---------- simulation ----------
const rndBern = p => (Math.random() < p ? 1 : 0);
const gaussPdf = (x, m, s) => { if (!isFinite(s) || s === 0) return 0; const z = (x - m) / s; return Math.exp(-0.5*z*z) / (Math.sqrt(2*Math.PI)*s); };

function simulateAndDrawOnePath(N, p, maps){
  const {xmap, ymap} = maps;
  let acc = 0;
  ctx.beginPath();
  ctx.moveTo(xmap(0), ymap(0));
  for (let t=1;t<=N;t++){
    acc += rndBern(p);
    ctx.lineTo(xmap(t), ymap(acc));
  }
  ctx.lineWidth = 1.2;
  ctx.strokeStyle = colors.line;
  ctx.globalAlpha = 0.14;
  ctx.lineJoin = 'round';
  ctx.lineCap = 'round';
  ctx.stroke();
  ctx.globalAlpha = 1;
  return acc;
}

// ---- progress helpers ----
function showProgress(show){
  if (!ui.progress) return;
  ui.progress.style.display = show ? 'flex' : 'none';
  ui.progress.setAttribute('aria-hidden', show ? 'false' : 'true');
}
function setProgress(frac){
  if (!ui.progressBar || !ui.progressLabel) return;
  const clamped = Math.max(0, Math.min(1, frac || 0));
  const pct = Math.round(clamped * 100);
  ui.progressBar.style.width = pct + '%';
  ui.progressLabel.textContent = pct + '%';
}

// ---- cancellation token to interrupt run() on Reset ----
let runToken = 0;

// ---------- run (always animated) ----------
async function run(){
  ensureCanvas();
  resizeCanvas();

  const N = clamp(int(ui.N.value,200), 1, 40000);
  const PATHS = clamp(int(ui.K.value,200), 1, 100000);
  const p = clamp(num(ui.P.value, .5), 0, 1);
  const lag = Math.max(0, int(ui.delay.value, 20));

  const d = dims;
  const maps = makeMaps(N, N, d);

  const myToken = ++runToken;

  // draw frame and mean line
  drawFrame(N, d);
  {
    const pts = [];
    for (let t=0;t<=N;t++) pts.push([maps.xmap(t), maps.ymap(t*p)]);
    polyline(pts, colors.mean, 2, 1);
  }

  // progress
  showProgress(true); setProgress(0);
  const bins = new Uint32Array(N+1);
  ui.note.textContent = 'Simulating...';

  // disable inputs during run
  setButtonsDisabled(true);

  try{
    // animated loop only
    let done = 0;
    const batch = 5;
    const pause = Math.max(10, lag);
    while (done < PATHS){
      if (myToken !== runToken) throw new Error('cancelled');
      const until = Math.min(done + batch, PATHS);
      for (let i=done; i<until; i++){
        const fin = simulateAndDrawOnePath(N, p, maps);
        bins[fin]++;
      }
      done = until;
      ui.note.textContent = `Simulated ${done}/${PATHS} paths...`;
      setProgress(done / PATHS);
      await sleep(pause);
    }

    if (myToken !== runToken) throw new Error('cancelled');

    // histogram + gaussian
    drawHistogramAndGaussian(bins, PATHS, N, p, maps, d);

    // stats
    const total = bins.reduce((a,b)=>a+b,0);
    let mean = 0; for (let k=0;k<=N;k++) mean += k * bins[k]; mean /= (total || 1);
    const mu = N * p, sigma = Math.sqrt(N * p * (1-p));
    ui.note.textContent = `Paths: ${PATHS} · N: ${N} · p=${p.toFixed(2)} — empirical mean(Y_N)=${mean.toFixed(3)}; theory: μ=${mu.toFixed(3)}, σ=${sigma.toFixed(3)}.`;
    setProgress(1);
  } catch(e){
    if (!(e && e.message === 'cancelled')){
      console.error(e);
      ui.note.textContent = 'Error during simulation.';
    }
  } finally{
    showProgress(false);
    setButtonsDisabled(false);
  }
}

function drawHistogramAndGaussian(bins, PATHS, N, p, maps, d){
  const {xmap, baseY, plotW, plotH} = maps;

  const histMaxPx = plotH * 0.35;
  let maxBin = 0; for (let k=0;k<=N;k++) if (bins[k] > maxBin) maxBin = bins[k];
  const countToPx = maxBin ? (histMaxPx / maxBin) : 0;

  const binW = Math.max(2, plotW / (N+1));
  ctx.save();
  ctx.fillStyle = colors.hist;
  for (let k=0;k<=N;k++){
    const c = bins[k]; if (!c) continue;
    const cx = xmap(k), hpx = c * countToPx, yTop = baseY - hpx;
    ctx.fillRect(cx - binW*0.42, yTop, binW*0.84, hpx);
  }

  const mu = N * p, sigma = Math.sqrt(N * p * (1-p));
  ctx.beginPath(); let started = false;
  for (let k=0;k<=N;k++){
    const expected = gaussPdf(k, mu, sigma) * PATHS;
    const hpx = expected * countToPx;
    const y = baseY - hpx, x = xmap(k);
    if (!started){ ctx.moveTo(x,y); started = true; } else ctx.lineTo(x,y);
  }
  ctx.lineWidth = 2.4; ctx.strokeStyle = colors.gauss; ctx.stroke();
  ctx.restore();
}

// ---------- helpers ----------
function int(v,def){ const n = parseInt(v,10); return Number.isFinite(n)?n:def; }
function num(v,def){ const n = parseFloat(v); return Number.isFinite(n)?n:def; }
function clamp(x, a, b){ return Math.max(a, Math.min(b, x)); }
function sleep(ms){ return new Promise(r=>setTimeout(r, ms)); }
function setButtonsDisabled(dis){
  ui.go.disabled = dis; ui.wipe.disabled = false;
  ui.N.disabled = dis; ui.K.disabled = dis; ui.P.disabled = dis; ui.delay.disabled = dis;
}

// ---------- UI ----------
ui.go.addEventListener('click', run);
ui.wipe.addEventListener('click', ()=>{
  runToken++; // cancel ongoing run
  ensureCanvas(); resizeCanvas();
  drawFrame(int(ui.N.value,200), dims);
  ui.note.textContent = 'Cleared.';
  setProgress(0); showProgress(false);
});

// initial load: visible empty chart
document.addEventListener('DOMContentLoaded', ()=>{
  ensureCanvas();
  resizeCanvas();
  drawFrame(int(ui.N.value,200), dims);
  ui.note.textContent = 'Ready. Set params and click Run.';
});
</script>
</body>
</html>

---