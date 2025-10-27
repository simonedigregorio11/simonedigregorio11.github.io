---
title: "Homework 3"
layout: default
permalink: /homework3/
---

<link rel="stylesheet" href="/assets/css/pages/homework3.css">

[← Back to Home](/)

---

# **RSA ecncode/decode using distributions**
# **Introduction**
This project presents a client-side **HTML/JavaScript** application that implements a simplified **RSA cryptosystem**
combined with a **frequency-based decryption**.
The tool operates over a 27-symbol alphabet, representing letters A..Z plus the space character, and allows users to
explore the main principles of RSA encryption, decryption, and statistical language analysis.

## **Overview and Objectives**
The objective of this project is to create a small yet complete **RSA demonstration tool**.
It enables users to generate RSA keys, encrypt plaintext, analyze the resulting ciphertext, and attempt decryption through an assisted **frequency-based analysis**.

By integrating concepts from **number theory** and **statistics**, the app shows how **language patterns** can be exploited in **cryptanalysis**.
The system is written in **pure JavaScript**, without any external dependencies.

Two working modes are supported:

- **pq-mode**, where the user inputs the private key components (**p**, **q**, and **e**);
  
- **public-key mode**, where only **n** and **e** are known, and the system attempts decryption by scanning possible values of **d**.

## **Core Structure and Implementation**
At its core, the system is based on the classic **RSA formulas**:
each character is converted into a numeric value between **0 and 26**, corresponding respectively to **A..Z** and **space**.

<div class="formula-box">
c = m<sup>e</sup> mod n
</div>

and decryption applies:

<div class="formula-box">
m = c<sup>d</sup> mod n
</div>

In pq-mode, the user provides the prime numbers **p** and **q** and the public exponent **e**. The program computes **n = p·q**, the
totient **φ(n) = (p−1)(q−1)**, and the private exponent **d** as the modular inverse of e modulo **φ(n)**.
This mode displays all the computed parameters (n, φ(n), d) in the interface and allows direct encryption of plaintext.

In public-key mode, instead, the user only enters **n** and **e**, and the ciphertext can be manually inserted.
Since d is unknown, the system performs a brute-force search across a defined range of d values, attempting to find
those that yield readable plaintext.

## **Key Features of the Application**
When users enter valid primes **p** and **q**, the system calculates the public and private parameters and shows them in KPI boxes (**n**, **φ(n)**, **d**).
It immediately validates whether **p** and **q** are prime, whether **e** is coprime with **φ(n)**, and whether **n** is large enough to include all 27 symbols.

The **frequency analyzer** computes the distribution of both plaintext symbols and ciphertext tokens and visualizes them through two **canvas charts**.
During decryption, the application tests a range of **d values**, reconstructs possible plaintexts, and ranks them by a **chi-squared score** compared to expected English letter frequencies.
The most plausible results are listed in a modal interface, allowing the user to select and inspect them.

## **User Interface and Visualization**
The interface is designed to make the cryptographic process interactive and intuitive.
The user can generate random key pairs, write a plaintext message, and instantly view its ciphertext representation. Two
**charts**, rendered on HTML canvas elements, display the **frequency distribution** of plaintext letters and ciphertext tokens.
Each computation step updates the interface dynamically.

When decrypting, the user may choose a maximum limit for **d** values to test, balancing performance and accuracy.
To help users understand key relationships, the values of n, φ(n), and d are displayed as KPI pills in the main section,
while warnings guide the user in case of non-prime inputs or inconsistent parameters.

## **RSA Tool with Frequency-Based Cryptanalysis**

<div class="hw3">
  <div class="cipher-container">

    <div class="row">
      <button class="load-example" id="btnExample">Load example</button>
      <button class="reset" id="btnReset">Reset</button>
    </div>

    <div class="card">
      <div class="card-h">
        <h3>Plaintext</h3>
      </div>
      <div class="card-c">
        <label for="plaintext">Input</label>
        <textarea id="plaintext" placeholder="Letters A..Z and spaces only. Others will be dropped."></textarea>

        <div class="grid" id="pqGrid">
          <div>
            <label for="p">Prime p</label>
            <input type="text" id="p" value="43" />
          </div>
          <div>
            <label for="q">Prime q</label>
            <input type="text" id="q" value="47" />
          </div>
          <div>
            <label for="e">Public exponent e</label>
            <input type="text" id="e" value="5" />
          </div>
        </div>

        <div class="kpi">
          <div class="pill" id="nPill">n = <span class="mono" id="nOut">—</span></div>
          <div class="pill" id="phiPill">φ(n) = <span class="mono" id="phiOut">—</span></div>
          <div class="pill" id="dPill">d = <span class="mono" id="dOut">—</span></div>
        </div>

        <div class="grid2" id="genRow">
          <div class="row">
            <button class="button" id="btnGenPQ">Generate</button>
            <button class="button" id="btnEncrypt">Encrypt →</button>
          </div>
        </div>

        <!-- WARN PANEL PLAINTEXT-->
        <div id="warnPlain" class="note"></div>

        <div class="row small">
          <span class="muted">Note: the space character is encoded as <code>26</code>.</span>
        </div>
      </div>
    </div>

    <div class="card">
      <div class="card-h">
        <h3>Ciphertext</h3>
      </div>
      <div class="card-c">

        <!-- Toggle key mode and locks -->
        <label class="small">
          <input type="checkbox" id="modeToggle">
          Manual ciphertext input
        </label>

        <label for="ciphertext">Output / Input</label>
        <textarea id="ciphertext" placeholder="e.g., 123 5 77 26 8 ... (26 represents a space)"></textarea>

        <div class="row small">
          <div class="nTokens-container">
            <span class="muted">Distinct tokens in ciphertext: <span class="mono" id="distinctTok">0</span></span>
          </div>
        </div>

        <!-- n,e PUBLIC: ALWAYS VISIBLE -->
        <div class="grid" id="neGrid">
          <div>
            <label for="nPublic">Modulus n (public)</label>
            <input type="text" id="nPublic" value="2021" />
          </div>
          <div>
            <label for="ePublic">Exponent e (public)</label>
            <input type="text" id="ePublic" value="5" />
          </div>
        </div>

        <div class="grid" id="maxDGrid">
          <div>
            <label for="maxD">Max d</label>
            <input type="text" id="maxD" value="2000" min="1" />
          </div>
        </div>

        <div class="row">
          <button class="button" id="btnDecrypt">Decrypt</button>
        </div>

        <!-- WARN PANEL CIPHERTEXT-->
        <div id="warnCipher" class="note"></div>

        <div class="chosen-container" id="chosenWrap">
          <div class="muted">Selected Decrypt Preview</div>
          <div class="preview mono" id="chosenText"></div>
          <div class="muted" id="chosenScore"></div>
        </div>
      </div>
    </div>

    <!-- Charts -->
    <section class="card">
      <div class="card-h">
        <h3>Distributions</h3>
      </div>
      <div class="card-c">
        <div class="charts">
          <div class="chart-card">
            <div class="muted">Plaintext frequency (A..Z + ␣)</div>
            <canvas id="plaintextChart" aria-label="Plaintext symbol frequency chart" role="img"></canvas>
          </div>
          <div class="chart-card">
            <div class="muted">Ciphertext frequency</div>
            <canvas id="ciphertextChart" aria-label="Ciphertext token frequency chart" role="img"></canvas>
          </div>
        </div>
      </div>
    </section>
  </div>

  <!-- Modal for top candidates -->
  <div class="modal-backdrop" id="modal">
    <div class="modal">
      <div class="modal-h">
        <div><strong>Pick the best decoding (candidates)</strong></div>
        <button class="btn ghost" id="btnCloseModal">✕</button>
      </div>
      <div class="modal-c">
        <div class="cand-grid" id="candGrid"></div>
      </div>
    </div>
  </div>
</div>

<script>
  // =========================
  //   RSA frequency tools
  // =========================

  // ---- WARN HELPERS ----
  const $id = (s) => document.getElementById(s);
  function setWarn(msgs) {
    const w = $id('warnPlain');
    if (!msgs || (Array.isArray(msgs) && msgs.length === 0)) {
      w.style.display = 'none';
      w.textContent = '';
      return;
    }
    const txt = Array.isArray(msgs) ? msgs.join('\n') : String(msgs);
    w.textContent = txt;
    w.style.display = 'block';
  }
  const warn = (msg) => setWarn(msg);

  // --- WARN Ciphertext ---
  function setWarnCipher(msgs) {
    const w = document.getElementById('warnCipher');
    if (!w) return;
    if (!msgs || (Array.isArray(msgs) && msgs.length === 0)) {
      w.style.display = 'none';
      w.textContent = '';
      return;
    }
    const txt = Array.isArray(msgs) ? msgs.join('\n') : String(msgs);
    w.textContent = txt;
    w.style.display = 'block';
  }

  const SPACE_CODE = 26;
  const ACode = "A".charCodeAt(0);

  // Extended Euclidean algorithm
  function egcd(a, b) {
    if (a === 0) return [b, 0, 1];
    const r = egcd(b % a, a);
    const g = r[0], y = r[1], x = r[2];
    return [g, x - Math.floor(b / a) * y, y];
  }
  // Modular inverse a^{-1} mod m
  function modInv(a, m) {
    const r = egcd(a, m);
    if (r[0] !== 1) return null;
    let x = r[1] % m;
    if (x < 0) x += m;
    return x;
  }
  // Fast modular exponentiation
  function modPow(base, exp, mod) {
    let res = 1 % mod, b = base % mod, e = exp;
    while (e > 0) {
      if (e & 1) res = (res * b) % mod;
      b = (b * b) % mod;
      e >>= 1;
    }
    return res;
  }
  // Naïve primality test (sufficient for small toys)
  function isPrime(n) {
    if (n < 2) return false;
    if (n % 2 === 0) return n === 2;
    for (let i = 3; i * i <= n; i += 2) if (n % i === 0) return false;
    return true;
  }
  // Greatest common divisor
  function gcd(a, b) { while (b) { const t = a % b; a = b; b = t; } return Math.abs(a); }

  // English letter frequency (A..Z) for chi-squared scoring
  const EN_FREQ = { A: 8.167, B: 1.492, C: 2.782, D: 4.253, E: 12.702, F: 2.228, G: 2.015, H: 6.094, I: 6.966, J: 0.153, K: 0.772, L: 4.025, M: 2.406, N: 6.749, O: 7.507, P: 1.929, Q: 0.095, R: 5.987, S: 6.327, T: 9.056, U: 2.758, V: 0.978, W: 2.360, X: 0.150, Y: 1.974, Z: 0.074 };

  // Sanitize plaintext: keep A..Z and single spaces
  function sanitizePlaintext(s) { return s.toUpperCase().replace(/[^A-Z\s]/g, '').replace(/\s+/g, ' ').trim(); }
  // Map letter → number (A=0..Z=25, space=26)
  function letterToNum(ch) { if (ch === ' ') return SPACE_CODE; return ch.charCodeAt(0) - ACode; }
  // Map number → letter (0..25→A..Z, 26→space)
  function numToLetter(n) { if (n === SPACE_CODE) return ' '; return String.fromCharCode(ACode + n); }
  // Keep only letters A..Z (used for chi-squared; spaces ignored)
  function onlyLetters(text) { return (text || '').toUpperCase().replace(/[^A-Z]/g, ''); }

  // Split ciphertext into integers (space or hyphen separated)
  function tokenizeCiphertext(s) {
    return s.trim().split(/[\s\-]+/).filter(Boolean).map(t => parseInt(t, 10)).filter(Number.isFinite);
  }
  function detokenizeCipher(nums) { return nums.join(' '); }

  // Chi-squared score (lower is better) comparing observed vs. English frequencies
  function chiSquaredScore(text) {
    const clean = onlyLetters(text);
    if (!clean.length) return Number.POSITIVE_INFINITY;
    const counts = {};
    for (const ch of clean) counts[ch] = (counts[ch] || 0) + 1;
    const n = clean.length;
    let chi = 0;
    for (const [ch, expPct] of Object.entries(EN_FREQ)) {
      const obs = counts[ch] || 0;
      const exp = (expPct / 100) * n;
      if (exp > 0) chi += ((obs - exp) ** 2) / exp;
    }
    return chi;
  }

  const $ = (s) => document.querySelector(s);
  const plaintextEl = $('#plaintext');
  const ciphertextEl = $('#ciphertext');

  // SINGLE CHECKBOX (below ciphertext) controlling key mode and plaintext lock
  const modeToggle = $('#modeToggle');

  // p,q,e inputs
  const pEl = $('#p');
  const qEl = $('#q');
  const eEl = $('#e');

  // n,e (public) inputs
  const nPublicEl = $('#nPublic');
  const ePublicEl = $('#ePublic');

  // UI blocks (grids/rows)
  const pqGrid = $('#pqGrid');
  const neGrid = $('#neGrid');
  const genRow = $('#genRow');
  const btnGenPQ = $('#btnGenPQ');

  // KPIs
  const nOut = $('#nOut');
  const phiPill = $('#phiPill');
  const dPill = $('#dPill');
  const phiOut = $('#phiOut');
  const dOut = $('#dOut');
  const nPill = $('#nPill');

  // Decrypt preview UI
  const chosenWrap = $('#chosenWrap');
  const chosenText = $('#chosenText');
  const chosenScore = $('#chosenScore');

  const btnEncrypt = $('#btnEncrypt');
  const btnReset = $('#btnReset');
  const btnExample = $('#btnExample');
  const btnDecrypt = $('#btnDecrypt');
  const maxDInput = $('#maxD');
  const distinctTok = $('#distinctTok');

  const modal = $('#modal');
  const btnCloseModal = $('#btnCloseModal');
  const candGrid = $('#candGrid');

  // Canvas helpers (HiDPI-aware; simple bespoke charts)
  function setupHiDPI(canvas) {
    const dpr = window.devicePixelRatio || 1;
    const rect = canvas.getBoundingClientRect();
    const cssW = Math.max(1, Math.round(rect.width));
    const cssH = Math.max(1, Math.round(rect.height));
    if (canvas.width !== Math.round(cssW * dpr) || canvas.height !== Math.round(cssH * dpr)) {
      canvas.width = Math.round(cssW * dpr);
      canvas.height = Math.round(cssH * dpr);
    }
    const ctx = canvas.getContext('2d');
    ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
    return { ctx, dpr, cssW, cssH };
  }
  // Draw axes and x-labels only (empty-state)
  function drawAxesOnly(canvas, xLabels = []) {
    const { ctx, dpr } = setupHiDPI(canvas);
    const W = canvas.width / dpr, H = canvas.height / dpr;
    ctx.clearRect(0, 0, W, H);
    const pad = { top: 12, right: 10, bottom: 32, left: 40 };
    const plotW = W - pad.left - pad.right;
    const plotH = H - pad.top - pad.bottom;
    const px = 0.5;
    ctx.strokeStyle = '#63c0f5'; ctx.lineWidth = 1;
    ctx.beginPath();
    ctx.moveTo(pad.left + px, pad.top + px);
    ctx.lineTo(pad.left + px, pad.top + plotH + px);
    ctx.lineTo(pad.left + plotW + px, pad.top + plotH + px);
    ctx.stroke();
    if (xLabels.length) {
      ctx.fillStyle = '#eaeaea'; ctx.font = '10px Monaco, Consolas, monospace';
      ctx.textAlign = 'center'; ctx.textBaseline = 'top';
      const barGap = 2; const barW = Math.max(1, Math.floor(plotW / xLabels.length) - barGap);
      for (let i = 0; i < xLabels.length; i++) {
        const x = pad.left + i * (barW + barGap) + barW / 2 + 1;
        ctx.fillText(xLabels[i], x, pad.top + plotH + 4);
      }
    }
    ctx.textAlign = 'right'; ctx.textBaseline = 'middle'; ctx.fillStyle = '#eaeaea';
    for (let y = 0; y <= 10; y++) {
      const yy = pad.top + plotH - ((y / 10) * (plotH - 10));
      ctx.fillText(String(y), pad.left - 6, yy);
    }
  }
  // Minimal bar chart (no libraries)
  function drawBarChart(canvasId, labels, counts, title = '') {
    const canvas = document.getElementById(canvasId);
    const { ctx, dpr } = setupHiDPI(canvas);
    const W = canvas.width / dpr, H = canvas.height / dpr;
    ctx.clearRect(0, 0, W, H);
    const pad = { top: 16, right: 10, bottom: 32, left: 40 };
    const plotW = W - pad.left - pad.right;
    const plotH = H - pad.top - pad.bottom;
    const px = 0.5;
    if (title) {
      ctx.fillStyle = '#b5e853'; ctx.font = '13px Monaco, Consolas, monospace';
      ctx.textAlign = 'left'; ctx.textBaseline = 'alphabetic';
      ctx.fillText(title, pad.left + 2, pad.top - 4);
    }
    const maxCount = Math.max(10, ...(counts || [0]));
    const yTop = maxCount;
    ctx.strokeStyle = '#63c0f5'; ctx.lineWidth = 1;
    ctx.beginPath();
    ctx.moveTo(pad.left + px, pad.top + px);
    ctx.lineTo(pad.left + px, pad.top + plotH + px);
    ctx.lineTo(pad.left + plotW + px, pad.top + plotH + px);
    ctx.stroke();
    const N = Math.max(1, labels.length);
    const barGap = 2; const barW = Math.max(1, Math.floor(plotW / N) - barGap);
    ctx.fillStyle = '#b5e853';
    for (let i = 0; i < N; i++) {
      const v = counts[i] || 0;
      const h = (v / yTop) * (plotH - 10);
      const x = pad.left + i * (barW + barGap) + 1;
      const y = pad.top + plotH - h;
      ctx.fillRect(x, y, barW, h);
    }
    ctx.fillStyle = '#eaeaea'; ctx.font = '10px Monaco, Consolas, monospace';
    ctx.textAlign = 'center'; ctx.textBaseline = 'top';
    for (let i = 0; i < N; i++) {
      const x = pad.left + i * (barW + barGap) + barW / 2 + 1;
      ctx.fillText(labels[i], x, pad.top + plotH + 4);
    }
    // Y-axis ticks: choose a step based on max
    ctx.textAlign = 'right';
    ctx.textBaseline = 'middle';
    ctx.fillStyle = '#eaeaea';
    ctx.strokeStyle = 'rgba(99,192,245,0.25)';
    ctx.lineWidth = 1;

    let step = 1;
    if (yTop > 200) step = 100;
    else if (yTop > 80) step = 50;
    else if (yTop > 40) step = 10;
    else if (yTop > 20) step = 5;
    else step = 1;

    const maxTick = Math.ceil(yTop / step) * step;

    for (let y = 0; y <= maxTick; y += step) {
      const yy = pad.top + plotH - ((y / maxTick) * (plotH - 10));
      ctx.beginPath();
      ctx.moveTo(pad.left + px, yy + px);
      ctx.lineTo(pad.left + plotW + px, yy + px);
      ctx.stroke();
      ctx.fillText(String(y), pad.left - 6, yy);
    }
  }

  // Frequency of symbols in plaintext (A..Z + space)
  function symbolFrequencies(text) {
    const s = sanitizePlaintext(text || '');
    const counts = new Array(SPACE_CODE + 1).fill(0);
    for (const ch of s) { counts[letterToNum(ch)]++; }
    return counts;
  }
  // Top token frequencies in ciphertext (and distinct count)
  function tokenFrequencies(nums, topN = 20) {
    const m = new Map();
    nums.forEach(n => m.set(n, (m.get(n) || 0) + 1));
    const arr = Array.from(m.entries()).sort((a, b) => b[1] - a[1]).slice(0, topN);
    return { labels: arr.map(([t]) => String(t)), counts: arr.map(([_, c]) => c), distinct: m.size };
  }
  // Redraw both charts
  function updateCharts() {
    const pCanvas = document.getElementById('plaintextChart');
    const labels27 = [...Array(26)].map((_, i) => String.fromCharCode(ACode + i)).concat('␣');
    const counts27 = symbolFrequencies(plaintextEl.value || '');
    if (!counts27.some(v => v > 0)) drawAxesOnly(pCanvas, labels27);
    else drawBarChart('plaintextChart', labels27, counts27, '');

    const cCanvas = document.getElementById('ciphertextChart');
    const nums = tokenizeCiphertext(ciphertextEl.value || '');
    if (nums.length === 0) { drawAxesOnly(cCanvas, []); distinctTok.textContent = '0'; return; }
    const tf = tokenFrequencies(nums, 20);
    drawBarChart('ciphertextChart', tf.labels, tf.counts, '');
    distinctTok.textContent = String(tf.distinct);
  }

  // ======= Mode & locks =======
  function currentMode() { return modeToggle.checked ? 'ne' : 'pq'; }

  // *** UPDATED ***: hide/show the KPIs n, φ(n), d based on the checkbox
  function updateKeyModeUI() {
    // grids always visible
    pqGrid.style.display = 'grid';
    neGrid.style.display = 'grid';
    btnGenPQ.style.display = 'flex';

    const m = currentMode();

    if (m === 'ne') {
      // Public-key mode: lock plain and p,q,e — enable cipher, nPublic, ePublic
      plaintextEl.readOnly = true;
      pEl.readOnly = true; qEl.readOnly = true; eEl.readOnly = true;

      ciphertextEl.readOnly = false;
      nPublicEl.readOnly = false; ePublicEl.readOnly = false;

      // HIDE KPIs
      nPill.style.display = 'none';
      phiPill.style.display = 'none';
      dPill.style.display = 'none';
    } else {
      // p,q,e mode: enable plain, p, q, e, nPublic, ePublic — lock cipher
      plaintextEl.readOnly = false;
      pEl.readOnly = false; qEl.readOnly = false; eEl.readOnly = false;

      ciphertextEl.readOnly = true;
      nPublicEl.readOnly = false; ePublicEl.readOnly = false;

      // SHOW KPIs
      nPill.style.display = 'inline-flex';
      phiPill.style.display = 'inline-flex';
      dPill.style.display = 'inline-flex';
    }

    chosenWrap.style.display = 'none';
    parseKeys(); // recompute KPIs (even if hidden)
  }

  // Parse keys + (in pq mode) auto-fill nPublic/ePublic
  function parseKeys() {
    const m = currentMode();
    let n = NaN, phi = NaN, d = null, e = NaN, err = "";
    const msgs = [];

    if (m === 'pq') {
      const p = Number(pEl.value || 0), q = Number(qEl.value || 0);
      e = Number(eEl.value || 0);
      n = p * q;
      phi = (p - 1) * (q - 1);

      if (!isPrime(p)) { err = 'p is not prime'; msgs.push(err); }
      else if (!isPrime(q)) { err = 'q is not prime'; msgs.push(err); }
      else if (p === q) { err = 'p and q must differ'; msgs.push(err); }
      else if (n <= SPACE_CODE) { err = 'n must be > 26 (to include space)'; msgs.push(err); }
      else if (phi <= 0) { err = 'Invalid φ(n)'; msgs.push(err); }
      else if (!e || e <= 0) { err = 'invalid e'; msgs.push(err); }
      else if (gcd(e, phi) !== 1) { err = 'e must be coprime with φ(n)'; msgs.push(err); }
      else {
        d = modInv(e, phi);
        if (d == null) { err = 'No modular inverse for e mod φ(n)'; msgs.push(err); }
      }

      // Auto-fill nPublic/ePublic ONLY in pq mode
      if (!err) {
        if (String(nPublicEl.value) !== String(n)) nPublicEl.value = String(n);
        if (String(ePublicEl.value) !== String(e)) ePublicEl.value = String(e);
      }
    } else {
      // Public mode: use nPublic/ePublic directly (no back-sync)
      n = Number(nPublicEl.value || 0);
      e = Number(ePublicEl.value || 0);
      if (!n || n <= SPACE_CODE) { err = 'n must be > 26 (to include space)'; msgs.push(err); }
      if (!e || e <= 0) { const m2 = 'invalid e'; err = err || m2; msgs.push(m2); }
      phi = NaN; d = null;
    }

    nOut.textContent = isFinite(n) ? String(n) : '—';
    phiOut.textContent = isFinite(phi) ? String(phi) : '—';
    dOut.textContent = d == null ? '—' : String(d);

    if (err) {
      if (m === 'ne') {
        setWarnCipher(msgs);
        setWarn('');
      } else {
        setWarn(msgs);
        setWarnCipher('');
      }
    } else {
      setWarn('');
      setWarnCipher('');
    }
    return { mode: m, n, phi, d, e, err };
  }

  // Key generation for pq-mode (random small primes + valid e)
  function randInt(a, b) { return Math.floor(Math.random() * (b - a + 1)) + a; }
  function randomPrime(min = 31, max = 173) {
    while (true) {
      let x = randInt(min, max);
      if (x % 2 === 0) x++;
      while (x <= max && !isPrime(x)) x += 2;
      if (x <= max) return x;
    }
  }
  function pickE(phi) {
    const candidates = [3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47];
    for (const c of candidates) if (c < phi && gcd(c, phi) === 1) return c;
    for (let e = 53; e < Math.min(2000, phi); e += 2) if (gcd(e, phi) === 1) return e;
    return null;
  }
  function generatePQ() {
    for (let t = 0; t < 1000; t++) {
      const p = randomPrime(31, 173);
      let q = randomPrime(31, 173);
      if (q === p) continue;
      const n = p * q;
      if (n <= SPACE_CODE) continue;
      const phi = (p - 1) * (q - 1);
      const e = pickE(phi);
      if (!e) continue;
      pEl.value = String(p);
      qEl.value = String(q);
      eEl.value = String(e);
      parseKeys();
      warn(`New random keys generated:\np=${p}  q=${q}\nn=${n}\nφ(n)=${phi}\ne=${e}`);
      return;
    }
    warn('Could not generate a valid (p,q,e). Try again.');
  }

  // Encrypt / Decrypt
  function encrypt() {
    const K = parseKeys();
    if (K.err) { warn('Cannot encrypt: invalid key parameters.'); return; }
    if (K.mode === 'ne') { warn('Encrypt requires p,q,e (switch off manual ciphertext input).'); return; }
    const clean = sanitizePlaintext(plaintextEl.value || '');
    if (!clean) { ciphertextEl.value = ''; updateCharts(); warn('Plaintext is empty.'); return; }
    const nums = Array.from(clean).map(ch => modPow(letterToNum(ch), K.e, K.n));
    ciphertextEl.value = detokenizeCipher(nums);
    chosenWrap.style.display = 'none';
    updateCharts();
    setWarn('');
  }

  // Basic HTML escaper for candidate previews
  function escapeHtml(s) { return s.replace(/[&<>]/g, ch => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;' }[ch])); }

  // Brute-force d: keep those with enough valid symbols and good chi-squared; optionally enforce distinctness
  function bruteForceD(maxD = 2000, pctValidThreshold = 0.6) {
    const nums = tokenizeCiphertext(ciphertextEl.value || '');
    if (!nums.length) return [];
    const K = parseKeys();
    const n = Number(K.n);
    if (!n || n <= 0) return [];
    const distinctTokens = new Set(nums).size;
    const enforceDistinct = distinctTokens >= 26;
    const requiredDistinctLetters = Math.min(distinctTokens, 26);

    const out = [];
    const hardCap = Math.min(maxD, 200000);
    for (let d = 1; d <= hardCap; d++) {
      let valid = 0;
      const letters = new Array(nums.length);
      for (let i = 0; i < nums.length; i++) {
        const m = modPow(nums[i], d, n);
        if (m >= 0 && m <= 26) { valid++; letters[i] = numToLetter(m); }
        else { letters[i] = null; }
      }
      const pctValid = valid / nums.length;
      if (pctValid < pctValidThreshold) continue;
      if (enforceDistinct) {
        const uniq = new Set(letters.filter(x => x && x !== ' ')).size;
        if (uniq < requiredDistinctLetters) continue;
      }
      const text = letters.map(v => v == null ? '?' : v).join('');
      const chi = chiSquaredScore(text);
      const uniqL = new Set(text.replace(/[^A-Z ]/g, '')).size; // info
      out.push({ d, pctValid, chi, text, uniq: uniqL });
    }
    out.sort((a, b) => {
      if (b.pctValid !== a.pctValid) return b.pctValid - a.pctValid;
      if (a.chi !== b.chi) return a.chi - b.chi;
      return b.uniq - a.uniq;
    });
    return out.slice(0, 20);
  }

  // Builds a candidate card with a 3-line preview + toggle
  function decrypt() {
    const m = Number(maxDInput.value) || 2000;
    candGrid.innerHTML = '';
    const nums = tokenizeCiphertext(ciphertextEl.value || '');
    if (!nums.length) { setWarnCipher('Ciphertext is empty. Paste space-separated integers.'); return; }

    const res = bruteForceD(m, 0.6);
    if (!res.length) { setWarnCipher('No candidates found. Try increasing maxD or verify n/e.'); return; }

    // Builds a candidate card with a 3-line preview + toggle
    function buildCandCard(c, idx) {
      const card = document.createElement('div');
      card.className = 'cand';
      card.innerHTML = `
      <div class="meta muted">
        #${idx + 1} • d=${c.d} • valid ${(c.pctValid * 100).toFixed(1)}% • chi-squared ${c.chi.toFixed(2)} • uniq ${c.uniq}
      </div>
      <div class="mono cand-text">${escapeHtml(c.text)}</div>
      <div class="actions">
        <button class="btn tiny btnToggle" type="button">Show more</button>
        <button class="btn tiny btnSelect" type="button" title="Use this candidate">Select</button>
      </div>
    `;

      const btnToggle = card.querySelector('.btnToggle');
      const btnSelect = card.querySelector('.btnSelect');
      const candText = card.querySelector('.cand-text');

      // Show the toggle only if the text is "long"
      const needsToggle = c.text.split('\n').length > 3 || c.text.length > 240;
      if (!needsToggle) btnToggle.style.display = 'none';

      btnToggle.addEventListener('click', (e) => {
        e.stopPropagation();
        const expanded = card.classList.toggle('expanded');
        btnToggle.textContent = expanded ? 'Show less' : 'Show more';
      });

      // Select the candidate (as before: closes the modal and shows the preview below)
      function selectCandidate() {
        chosenText.textContent = c.text;
        chosenScore.textContent =
          'candidate d: ' + c.d +
          ' • chi-squared: ' + c.chi.toFixed(2) +
          ' • valid: ' + (c.pctValid * 100).toFixed(1) + '% • uniq: ' + c.uniq;
        chosenWrap.style.display = 'block';
        hideModal();
        updateCharts();
        setWarn('');
        setWarnCipher('');
      }

      btnSelect.addEventListener('click', (e) => { e.stopPropagation(); selectCandidate(); });

      return card;
    }

    setWarnCipher(`Found ${res.length} candidates. Pick one to preview.`);
    res.forEach((c, idx) => candGrid.appendChild(buildCandCard(c, idx)));
    showModal();
  }


  // Modal helpers
  function showModal() { modal.classList.add('show'); }
  function hideModal() { modal.classList.remove('show'); }

  // Reset everything (back to pq-mode, clear fields, hide previews/warnings)
  function resetAll() {
    modeToggle.checked = false; // back to p,q,e
    updateKeyModeUI();

    plaintextEl.value = '';
    ciphertextEl.value = '';
    chosenWrap.style.display = 'none';
    setWarn('');
    setWarnCipher('');
    updateCharts();
  }

  // Load a reproducible example (in pq-mode) and encrypt it
  function loadExample() {
    modeToggle.checked = false; // example in pq-mode
    updateKeyModeUI();

    pEl.value = 43; qEl.value = 47; eEl.value = 5;
    parseKeys();
    plaintextEl.value = 'Cybersecurity professionals rely on a variety of statistical tools and techniques to analyze data, identify patterns, and predict threats. These methods range from basic descriptive statistics to advanced probabilistic and inferential models. Each of them serves a specific purpose in detecting anomalies, assessing risks, and supporting decision-making.';
    encrypt();
    warn('Example loaded with p=43, q=47, e=5.');
  }

  // Draw empty axes on first load
  function initEmptyCharts() {
    const labels27 = [...Array(26)].map((_, i) => String.fromCharCode(ACode + i)).concat('␣');
    drawAxesOnly(document.getElementById('plaintextChart'), labels27);
    drawAxesOnly(document.getElementById('ciphertextChart'), []);
  }

  // Wire up all events
  function attachEvents() {
    btnEncrypt.addEventListener('click', encrypt);
    btnReset.addEventListener('click', resetAll);
    btnExample.addEventListener('click', loadExample);
    btnCloseModal.addEventListener('click', hideModal);
    modal.addEventListener('click', (e) => { if (e.target === modal) hideModal(); });

    // MODE toggle (under ciphertext)
    modeToggle.addEventListener('change', () => {
      updateKeyModeUI();
      setWarn('');
      setWarnCipher('');
    });

    // inputs -> re-parse keys & messages
    pEl.addEventListener('input', () => { parseKeys(); });
    qEl.addEventListener('input', () => { parseKeys(); });
    eEl.addEventListener('input', () => { parseKeys(); });
    nPublicEl.addEventListener('input', () => { parseKeys(); });
    ePublicEl.addEventListener('input', () => { parseKeys(); });

    // generate (pq)
    document.getElementById('btnGenPQ').addEventListener('click', generatePQ);

    // decrypt & charts
    btnDecrypt.addEventListener('click', decrypt);
    ciphertextEl.addEventListener('input', () => { updateCharts(); setWarn(''); });
    plaintextEl.addEventListener('input', () => { updateCharts(); setWarn(''); });

    window.addEventListener('resize', updateCharts);
  }

  // Init
  updateKeyModeUI();        // set visibility and locks
  initEmptyCharts();
  updateCharts();
  parseKeys();
  attachEvents();
</script>
<br>

## **Key Mathematical Components**
The script implements all the fundamental algorithms of RSA from scratch:
The Extended Euclidean Algorithm (EGCD) for computing the greatest common divisor and modular inverses.
A modInv(a, m) function that returns the modular inverse of a modulo m, used to calculate the private key d.
The modPow(base, exp, mod) function implementing fast modular exponentiation via the square-and-multiply method,
ensuring efficiency even with many iterations of d.
A simple primality test based on trial division, sufficient for small primes used in this didactic example.
Each of these functions is deliberately implemented in a readable way rather than optimized, so that students can trace
the logic step by step.

## **Code Overview Screenshot**

![code](/assets/images/code_2.png)

## **Main Functions Explained**
Below are the most significant functions in the project, showing how the mathematics of RSA and the frequency analysis are implemented in JavaScript.

### **4.1 Modular Exponentiation — modPow()**
This function efficiently computes a<sup>b</sup> mod n using the **square-and-multiply** algorithm.
It’s crucial for both encryption and decryption, as direct exponentiation would be computationally expensive.

```js
function modPow(base, exp, mod) {
  let res = 1 % mod, b = base % mod, e = exp;
  while (e > 0) {
    if (e & 1) res = (res * b) % mod;
    b = (b * b) % mod;
    e >>= 1; // divide exponent by 2
  }
  return res;
}
```

At each step, the function squares the base and multiplies it into the result when the current bit of the exponent is 1.
This reduces the number of multiplications from **O(exp)** to **O(log exp)**, making RSA feasible even for large numbers.

### **4.2 Modular Inverse — modInv()**
This function computes the inverse of a number modulo m, using the **Extended Euclidean Algorithm (EGCD)**.
It is essential for calculating the private exponent **d = e⁻¹ mod φ(n)**.

```js
function modInv(a, m) {
  const r = egcd(a, m);
  if (r[0] !== 1) return null; // no inverse exists
  let x = r[1] % m;
  if (x < 0) x += m;
  return x;
}
```

It finds the coefficient x such that a⋅x ≡ 1 (mod m).
If **gcd(a,m) ≠ 1**, the inverse doesn’t exist, and **e** cannot be used as a valid exponent.

### **4.3 Chi-Squared Scoring — chiSquaredScore()**
This function compares the letter distribution of a candidate plaintext with known English frequencies, providing a **numerical score** that estimates linguistic similarity.

```js
function chiSquaredScore(text) {
  const clean = (text || '').toUpperCase().replace(/[^A-Z]/g, '');
  if (!clean.length) return Infinity;
  const counts = {}; for (const ch of clean) counts[ch] = (counts[ch] || 0) + 1;
  const n = clean.length;
  let chi = 0;
  for (const [ch, expPct] of Object.entries(EN_FREQ)) {
    const obs = counts[ch] || 0;
    const exp = (expPct / 100) * n;
    if (exp > 0) chi += ((obs - exp) ** 2) / exp;
  }
  return chi;
}
```

A lower **chi-squared value** indicates that the text’s letter frequencies are close to natural English.
This statistical test allows the system to automatically rank possible decryptions without requiring user intervention.

### **4.4 Brute-Force Decryption — bruteForceD()**
This function iterates through potential values of **d**, decrypts the ciphertext, and evaluates each result.
It filters candidates that contain at least 60% valid symbols and checks for distinct letter variety.

```js
function bruteForceD(maxD = 2000, pctValidThreshold = 0.6) {
  const nums = tokenizeCiphertext(ciphertextEl.value || '');
  if (!nums.length) return [];
  const n = Number(parseKeys().n);
  if (!n) return [];
  const results = [];
  for (let d = 1; d <= maxD; d++) {
    let valid = 0;
    const letters = nums.map(num => {
      const m = modPow(num, d, n);
      if (m >= 0 && m <= 26) { valid++; return numToLetter(m); }
      return '?';
    });
    const pctValid = valid / nums.length;
    if (pctValid >= pctValidThreshold) {
      const text = letters.join('');
      const chi = chiSquaredScore(text);
      results.push({ d, pctValid, chi, text });
    } 
  }
  return results.sort((a, b) => a.chi - b.chi).slice(0, 20);
}
```

It tests every possible **d** value within a chosen range, using **modPow()** for decryption and **chiSquaredScore()** for ranking.
The top 20 candidates are shown in the modal window, each with a statistical confidence score.

## **Frequency Analysis and Visualization**
Two dynamic **canvas charts** show the frequency distributions of plaintext letters and ciphertext tokens.
They are automatically updated on every interaction, making the transformation between readable text and numerical ciphertext visually clear.
This visualization helps users grasp how encryption randomizes letter patterns and why statistical techniques can still recover meaningful structure.

## **Conclusion**
In conclusion, this project unites **number theory**, **modular arithmetic**, and **statistical language modeling** in a
compact browser-based demonstrator.
It provides an intuitive environment to understand how the RSA algorithm operates and how the statistical properties of
natural language can assist in decryption when the key is unknown.

---