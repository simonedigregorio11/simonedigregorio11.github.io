---
title: "Homework 7"
layout: default
permalink: /homework7/
---

<link rel="stylesheet" href="/assets/css/pages/homework7.css">

[← Back to Home](/)

---

# **Random Walk and Binomial Convergence**

## **Introduction**

This assignment investigates a probabilistic model for a **server that receives weekly security updates** while being
targeted by multiple independent attackers. Each week, every attacker has a probability **p** of successfully breaching
the system. If **m** attackers are active, the probability that the system suffers *any* breach in a week becomes

<div class="formula-box">
    q = 1 - (1 - p)<sup>m</sup>
</div>

The server’s “security score” S<sub>t</sub> evolves over **n weeks** as a **random walk** taking steps:

<div class="formula-box">
    <div class="formula">
        X<sub>t</sub> =
        <span class="cases">
            <span class="cases-row">+1  if the server remains secure</span>
            <span class="cases-row">−1  if the server is breached</span>
        </span>
    </div><br>
    so that <strong>S<sub>t</sub> = Σ<sub>i=1</sub><sup>t</sup> X<sub>i</sub></strong>
</div>

The cumulative endpoint **S<sub>n</sub>** can take values in {n, n-2, ..., -n}.
Since breaches are independent, the probability of k breaches follows a **Binomial(n, q)** distribution, which—after
mapping S<sub>n</sub> = n - 2k describes the theoretical law of the random walk endpoints.
By simulating many independent runs, we can observe how the empirical distribution of S<sub>n</sub> converges to this
theoretical model as **n** and **R** grow.

---

## **Objectives**

- Simulate R independent trajectories of length n.

- Visualize these as **random-walk paths** showing the drift E[S<sub>t</sub>] = t(1−2q).

- Count how many trajectories end at each possible S<sub>n</sub>.

- Compare the empirical frequencies to the **Binomial(n, q)** distribution.

- Evaluate convergence numerically through **total variation distance**.

---

## **The Interactive Tool**

The implemented web tool provides:<br>

- Numeric inputs for *n*, *m*, *p*, *R*, and batch size.

- Real-time plotting of **trajectories** (left panel) and **endpoint distribution** (right panel).

- An animated accumulation process that shows convergence toward the theoretical curve.

- A data table displaying counts, empirical frequencies, and theoretical probabilities.

- Computed metrics: empirical mean/variance vs theoretical mean/variance, and total variation distance.

---

# **Demo**

<div class="hw7">
    <div class="card">
        <div class="row">
            <label>Weeks n</label><input id="n" type="number" min="1" value="80">
            <label>Attackers m</label><input id="m" type="number" min="1" value="5">
            <label>p (per attacker)</label><input id="p" type="number" min="0" max="1" step="0.01" value="0.30">
            <label>Runs R</label><input id="R" type="number" min="1" value="6000">
            <label>Batch size</label><input id="batch" type="number" min="1" value="500">
            <label>Paths shown</label><input id="keep" type="number" min="0" value="400"
                title="How many trajectories to draw (for speed)">
        </div>
        <div class="buttons">
            <button id="run">Run</button>
            <button class="danger" id="clear">Clear</button>
        </div>
        <div class="stats-container" id="statscontainer">
            <div class="stats" id="paramline"></div>
            <div class="stats" id="statline"></div>
        </div>
    </div>

    <div class="grid">
        <div class="card">
            <h3 style="margin:0 0 8px;">Random-Walk Trajectories</h3>
            <div class="legend">
                Parallel with a (possibly biased) ±1 random walk. Drift = E[X] = 1 − 2q.<br>
                Here p is per attacker and q = 1 − (1 − p)<sup>m</sup> is the weekly breach probability.
            </div>

            <div class="hr"></div>
            <canvas id="paths" width="1200" height="300" aria-label="Random-walk trajectories"></canvas>
        </div>

        <div class="card">
            <h3 style="margin:0 0 8px;">Endpoint Distribution at t = n</h3>
            <div class="legend">
                Bars = empirical counts/R for S<sub>n</sub> ∈ {n, n−2, …, −n}. Green line = Binomial(n,q) mapped via
                S<sub>n</sub> = n − 2k.
            </div>
            <div class="hr"></div>
            <canvas id="hist" width="1200" height="300" aria-label="Endpoint histogram"></canvas>
            <div id="tableWrap" class="legend" style="margin-top:8px">Run to view counts.</div>
        </div>
    </div>
    <script>
        /* =========================
           HiDPI-safe canvas prep
           ========================= */
        /* Returns a 2D context scaled so that 1 unit in canvas commands = 1 CSS pixel. */
        /* Global canvas-size lock to keep width/height stable while the run is animating */
        let CANVAS_LOCK = null; // {paths:{w,h}, hist:{w,h}} or null
        let RUN_ID = 0;

        function prepCanvas(canvas, key) {
            const dpr = window.devicePixelRatio || 1;

            // If a run is in progress, reuse the locked CSS size to avoid reflow-induced resizes
            let cssW, cssH;
            if (CANVAS_LOCK && CANVAS_LOCK[key]) {
                ({ w: cssW, h: cssH } = CANVAS_LOCK[key]);
            } else {
                const rect = canvas.getBoundingClientRect();
                cssW = Math.max(1, rect.width);
                cssH = Math.max(1, rect.height);
            }

            // Desired backing-store size (integer, HiDPI aware)
            const bw = Math.max(1, Math.floor(cssW * dpr));
            const bh = Math.max(1, Math.floor(cssH * dpr));

            // Update only when it actually changes (prevents context reset)
            if (canvas.width !== bw || canvas.height !== bh) {
                canvas.width = bw;
                canvas.height = bh;
            }

            const ctx = canvas.getContext('2d');
            // Set transform so that 1 canvas unit = 1 CSS pixel (guarded to avoid redundant work)
            const t = ctx.getTransform();
            if (t.a !== dpr || t.d !== dpr || t.b !== 0 || t.c !== 0 || t.e !== 0 || t.f !== 0) {
                ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
            }
            return { cssW, cssH, ctx };
        }


        /* =========================
           Math helpers & utilities
           ========================= */
        const clamp = (x, a, b) => Math.max(a, Math.min(b, x));

        /* Binomial coefficient C(n,k) via multiplicative formula (stable for moderate n) */
        function binom(n, k) {
            if (k < 0 || k > n) return 0;
            if (k === 0 || k === n) return 1;
            k = Math.min(k, n - k);
            let c = 1;
            for (let i = 1; i <= k; i++) c = (c * (n - k + i)) / i;
            return c;
        }

        /* Binomial PMF for K~Bin(n,q); returns Float64Array of length n+1 */
        function binomialPMF(n, q) {
            const pmf = new Float64Array(n + 1);
            for (let k = 0; k <= n; k++) pmf[k] = binom(n, k) * Math.pow(q, k) * Math.pow(1 - q, n - k);
            return pmf;
        }

        /* "Nice" linear ticks between min and max, aiming for ~targetCount labels */
        function niceTicks(min, max, targetCount = 6) {
            if (!isFinite(min) || !isFinite(max) || min === max) {
                const v = isFinite(min) ? min : 0;
                return [v - 1, v, v + 1];
            }
            const span = max - min;
            const raw = span / Math.max(1, targetCount);
            const pow10 = Math.pow(10, Math.floor(Math.log10(raw)));
            const candidates = [1, 2, 2.5, 5, 10].map(m => m * pow10);
            const step = candidates.reduce((best, c) => Math.abs(c - raw) < Math.abs(best - raw) ? c : best, candidates[0]);
            const niceMin = Math.floor(min / step) * step;
            const niceMax = Math.ceil(max / step) * step;
            const ticks = [];
            for (let v = niceMin; v <= niceMax + 1e-12; v += step) {
                const vv = Math.abs(v) < 1e-12 ? 0 : +v.toPrecision(12); // trim fp noise
                ticks.push(vv);
            }
            return ticks;
        }

        /* =========================
           Simulation
           ========================= */
        /* Simulates R walks of length n with step +1 (no breach) w.p. (1-q) and -1 (breach) w.p. q.
           Returns Map of endpoints and up to 'keep' full paths for drawing. */
        function simulate(n, R, q, keep = 400) {
            const kept = Math.min(R, keep);
            const paths = new Array(kept);
            for (let i = 0; i < kept; i++) paths[i] = new Int16Array(n + 1);

            const endpoints = new Map(); // key = S_n ∈ {-n,-n+2,...,n}
            for (let r = 0; r < R; r++) {
                let s = 0;
                const store = (r < kept);
                if (store) paths[r][0] = 0;
                for (let t = 1; t <= n; t++) {
                    s += (Math.random() < q) ? -1 : 1;  // −1 if breach, +1 otherwise
                    if (store) paths[r][t] = s;
                }
                endpoints.set(s, (endpoints.get(s) || 0) + 1);
            }
            return { paths: kept ? paths : [], endpoints };
        }

        /* =========================
           Drawing: trajectories with axes, ticks, grid (CLIPPED)
           ========================= */
        function drawPaths(paths, canvas) {
            const { cssW, cssH, ctx } = prepCanvas(canvas, "paths");
            ctx.clearRect(0, 0, cssW, cssH);
            if (!paths.length) return;

            const n = paths[0].length - 1;
            const padL = 60, padB = 34, padT = 14, padR = 16;
            const W = cssW - padL - padR;
            const H = cssH - padT - padB;
            const x0 = padL, y0 = cssH - padB;

            // Global min/max across all paths (for vertical scale)
            let gmin = 0, gmax = 0;
            for (const p of paths) {
                for (let t = 0; t <= n; t++) { gmin = Math.min(gmin, p[t]); gmax = Math.max(gmax, p[t]); }
            }
            if (gmin === gmax) { gmin--; gmax++; }

            const sx = W / n;
            const sy = H / (gmax - gmin);

            // Axes
            ctx.strokeStyle = "#e2e8f0"; ctx.lineWidth = 1;
            // x-axis
            ctx.beginPath(); ctx.moveTo(x0, y0); ctx.lineTo(x0 + W, y0); ctx.stroke();
            // y-axis
            ctx.beginPath(); ctx.moveTo(x0, y0 - H); ctx.lineTo(x0, y0); ctx.stroke();

            // X ticks (time t), aim for ~10 labels
            ctx.fillStyle = "#e2e8f0"; ctx.font = "12px system-ui, Monaco, monospace";
            ctx.textAlign = "center"; ctx.textBaseline = "top";
            const xStep = Math.max(1, Math.round(n / 10));
            for (let t = 0; t <= n; t += xStep) {
                const x = x0 + t * sx;
                ctx.beginPath(); ctx.moveTo(x, y0); ctx.lineTo(x, y0 + 4); ctx.stroke();
                ctx.fillText(String(t), x, y0 + 6);
            }

            // Y ticks (S_t) with light horizontal grid — labels kept inside the frame
            {
                const yTicks = niceTicks(gmin, gmax, 6).filter(v => v >= gmin - 1e-9 && v <= gmax + 1e-9);
                const yTop = y0 - H, yBot = y0;

                ctx.textAlign = "right";
                ctx.font = "12px system-ui, Monaco, monospace";

                for (const v of yTicks) {
                    const yRaw = y0 - (v - gmin) * sy;
                    // Clamp grid/tick to the plotting area
                    const yGrid = Math.max(yTop, Math.min(yBot, yRaw));

                    // grid line
                    ctx.strokeStyle = "rgba(226,232,240,.25)";
                    ctx.lineWidth = 1;
                    ctx.beginPath(); ctx.moveTo(x0, yGrid); ctx.lineTo(x0 + W, yGrid); ctx.stroke();

                    // tick mark
                    ctx.strokeStyle = "#e2e8f0";
                    ctx.beginPath(); ctx.moveTo(x0 - 4, yGrid); ctx.lineTo(x0, yGrid); ctx.stroke();

                    // label: nudge inside if we’re on the very top/bottom edge
                    let yLabel = yGrid;
                    if (Math.abs(yGrid - yBot) < 0.75) { // bottom edge
                        ctx.textBaseline = "bottom";
                        yLabel = yBot - 2;
                    } else if (Math.abs(yGrid - yTop) < 0.75) { // top edge
                        ctx.textBaseline = "top";
                        yLabel = yTop + 2;
                    } else {
                        ctx.textBaseline = "middle";
                    }

                    const lab = Math.abs(v) < 1e-9 ? "0" : v.toFixed(0);
                    ctx.fillStyle = "#e2e8f0";
                    ctx.fillText(lab, x0 - 6, yLabel);
                }
            }

            // ---- CLIP: keep strokes strictly inside the plotting area ----
            ctx.save();
            // Exact plot rectangle (no half-pixel inset; avoids right-edge overflow when sx is fractional)
            ctx.beginPath();
            ctx.rect(x0, y0 - H, W, H);
            ctx.clip();

            // Paths (clipped)
            const xMax = x0 + W; // used for a gentle clamp to avoid anti-aliased bleed at the right edge
            for (const p of paths) {
                const grad = ctx.createLinearGradient(x0, 0, x0 + W, 0);
                grad.addColorStop(0, 'white'); grad.addColorStop(1, '#b5e853');
                ctx.strokeStyle = grad; ctx.lineWidth = 0.8; ctx.globalAlpha = 0.8;
                ctx.lineCap = "butt"; ctx.lineJoin = "miter";
                ctx.beginPath();
                for (let t = 0; t <= n; t++) {
                    // Clamp x by a tiny epsilon inside the frame to ensure last vertex never spills outside
                    const x = Math.min(xMax - 0.5, x0 + t * sx);
                    const y = y0 - (p[t] - gmin) * sy;
                    if (t === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
                }
                ctx.stroke();
            }
            ctx.globalAlpha = 1;
            ctx.restore();
            // ------------------------------------------------------

            // Thin frame to visually seal the plot area
            ctx.strokeStyle = "rgba(226,232,240,.6)";
            ctx.lineWidth = 1;
            // Align to pixel grid for crispness
            ctx.strokeRect(Math.round(x0) + 0.5, Math.round(y0 - H) + 0.5, Math.round(W) - 1, Math.round(H) - 1);

            // Tiny axis titles
            ctx.fillStyle = "#e2e8f0"; ctx.font = "12px system-ui, Monaco, monospace";
            ctx.textAlign = "left"; ctx.textBaseline = "alphabetic";
            ctx.fillText("t", x0 + W, y0 + 28);
            ctx.save();
            ctx.translate(x0 - 36, y0 - H + 20);
            ctx.rotate(-Math.PI / 2);
            ctx.fillText("S_t", 0, 0);
            ctx.restore();
        }


        /* =========================
           Drawing: histogram + theory with axes, ticks, grid
           ========================= */
        function drawHistogram(endpoints, n, R, pmf, canvas) {
            const { cssW, cssH, ctx } = prepCanvas(canvas, "hist");
            ctx.clearRect(0, 0, cssW, cssH);

            const padL = 60, padB = 34, padT = 14, padR = 16;
            const W = cssW - padL - padR;
            const H = cssH - padT - padB;
            const x0 = padL, y0 = cssH - padB;

            // Possible S_n values
            const scores = [];
            for (let s = -n; s <= n; s += 2) scores.push(s);

            // X scaling for discrete bars
            const barW = Math.max(2, Math.min(26, W / (scores.length + 2)));
            const xOf = s => x0 + ((s + n) / 2 + 1) * barW;

            // Max empirical frequency (for y scaling)
            let maxF = 0;
            for (const s of scores) {
                const f = (endpoints.get(s) || 0) / R;
                if (f > maxF) maxF = f;
            }
            if (maxF < 1e-12) maxF = 1;
            const yOfFreq = f => y0 - f * (H * 0.95 / maxF);

            // Axes
            ctx.strokeStyle = "#e2e8f0"; ctx.lineWidth = 1;
            ctx.beginPath(); ctx.moveTo(x0, y0); ctx.lineTo(x0 + W, y0); ctx.stroke(); // x-axis
            ctx.beginPath(); ctx.moveTo(x0, y0 - H); ctx.lineTo(x0, y0); ctx.stroke(); // y-axis

            // X ticks (s = n-2k)
            ctx.fillStyle = "#e2e8f0"; ctx.font = "12px system-ui, Monaco, monospace";
            ctx.textAlign = "center"; ctx.textBaseline = "top";
            const stepS = Math.max(2, Math.floor(n / 10) * 2);
            for (let s = -n; s <= n; s += stepS) {
                const x = xOf(s);
                ctx.beginPath(); ctx.moveTo(x, y0); ctx.lineTo(x, y0 + 4); ctx.stroke();
                ctx.fillText(String(s), x, y0 + 6);
            }

            // Y ticks (frequencies) with horizontal grid
            const yTicks = niceTicks(0, maxF, 6);
            ctx.textAlign = "right"; ctx.textBaseline = "middle";
            for (const v of yTicks) {
                const y = yOfFreq(v);
                // grid
                ctx.strokeStyle = "rgba(226,232,240,.25)";
                ctx.beginPath(); ctx.moveTo(x0, y); ctx.lineTo(x0 + W, y); ctx.stroke();
                // tick
                ctx.strokeStyle = "#e2e8f0";
                ctx.beginPath(); ctx.moveTo(x0 - 4, y); ctx.lineTo(x0, y); ctx.stroke();
                // label
                const lab = (v >= 0.1 || maxF >= 0.5) ? v.toFixed(1) : v.toFixed(2);
                ctx.fillStyle = "#e2e8f0";
                ctx.fillText(lab, x0 - 6, y);
            }

            // Empirical bars
            ctx.fillStyle = "#ffffff";
            for (const s of scores) {
                const f = (endpoints.get(s) || 0) / R;
                const x = xOf(s), y = yOfFreq(f);
                ctx.fillRect(x - barW * 0.45, y, barW * 0.9, y0 - y);
            }

            // Theoretical polyline (Bin(n,q) mapped via s = n - 2k)
            ctx.strokeStyle = "#b5e853"; ctx.lineWidth = 2;
            ctx.beginPath(); let first = true;
            for (let k = 0; k <= n; k++) {
                const s = n - 2 * k;
                const prob = pmf[k];
                const x = xOf(s), y = yOfFreq(prob);
                if (first) { ctx.moveTo(x, y); first = false; } else ctx.lineTo(x, y);
            }
            ctx.stroke();

            // Tiny axis titles
            ctx.fillStyle = "#e2e8f0"; ctx.font = "12px system-ui, Monaco, monospace";
            ctx.textAlign = "left"; ctx.textBaseline = "alphabetic";
            ctx.fillText("S_n", x0 + W - 12, y0 + 28);
            ctx.save();
            ctx.translate(x0 - 36, y0 - H + 55);
            ctx.rotate(-Math.PI / 2);
            ctx.fillText("frequency", 0, 0);
            ctx.restore();
        }

        /* =========================
           Table & convergence metric
           ========================= */
        /* Builds an HTML table mapping k breaches ↔ s = n-2k, with empirical vs theoretical probabilities. */
        function renderTable(endpoints, n, R, pmf) {
            let html = `<table><thead>
    <tr><th>Score s = n − 2k</th><th>k breaches</th><th>Count</th><th>Empirical freq</th><th>Theoretical P</th></tr>
  </thead><tbody>`;
            for (let k = 0; k <= n; k++) {
                const s = n - 2 * k;
                const c = endpoints.get(s) || 0;
                const f = c / R;
                const p = pmf[k];
                html += `<tr><td>${s}</td><td>${k}</td><td>${c}</td><td>${f.toFixed(4)}</td><td>${p.toFixed(4)}</td></tr>`;
            }
            html += `</tbody></table>`;
            document.getElementById('tableWrap').innerHTML = html;
        }

        /* Total variation distance between empirical distribution of S_n and the mapped Binomial(n,q) */
        function totalVariation(endpoints, n, R, pmf) {
            let tv = 0;
            for (let k = 0; k <= n; k++) {
                const s = n - 2 * k;
                const f = (endpoints.get(s) || 0) / R;
                tv += Math.abs(f - pmf[k]);
            }
            return 0.5 * tv;
        }

        /* =========================
           Controller
           ========================= */
        const pathsC = document.getElementById('paths');
        const histC = document.getElementById('hist');

        let lastState = {
            paths: [],
            endpoints: new Map(),
            n: 0, R: 0, pmf: new Float64Array(0),
            q: 0
        };

        function setParamLine(n, m, p, q, R) {
            document.getElementById('paramline').innerHTML =
                `n = <strong>${n}</strong>, m = <strong>${m}</strong>, p = <strong>${p.toFixed(4)}</strong> → q = <strong>${q.toFixed(6)}</strong>, runs = <strong>${R}</strong>`;
        }

        function setStatsLine(n, q, R, endpoints) {
            // Empirical mean/var of S_n; theoretical mean/var for S_n
            let sum = 0, sumsq = 0;
            for (let k = 0; k <= n; k++) {
                const s = n - 2 * k;
                const c = endpoints.get(s) || 0;
                sum += s * c;
                sumsq += s * s * c;
            }
            const meanEmp = R ? sum / R : 0;
            const varEmp = R ? (sumsq / R - meanEmp * meanEmp) : 0;

            const meanTh = n * (1 - 2 * q);
            const varTh = 4 * n * q * (1 - q);

            document.getElementById('statline').innerHTML =
                `Empirical: E[Sn] = <strong>${meanEmp.toFixed(3)}</strong>, Var[Sn] = <strong>${varEmp.toFixed(3)}</strong> | Theoretical: E[Sn] = <strong>${meanTh.toFixed(3)}</strong>, Var[Sn] = <strong>${varTh.toFixed(3)}</strong>`;
        }

        document.getElementById('run').addEventListener('click', async () => {
            const myRun = ++RUN_ID;
            document.getElementById('statscontainer').style.display = 'block';
            const n = clamp(parseInt(document.getElementById('n').value, 10) || 0, 1, 20000);
            const m = clamp(parseInt(document.getElementById('m').value, 10) || 0, 1, 100000);
            const p = clamp(parseFloat(document.getElementById('p').value) || 0, 0, 1);
            const R = clamp(parseInt(document.getElementById('R').value, 10) || 0, 1, 200000);
            const keep = clamp(parseInt(document.getElementById('keep').value, 10) || 0, 0, R);
            const batch = clamp(parseInt(document.getElementById('batch').value, 10) || 500, 1, 10000);

            const q = 1 - Math.pow(1 - p, m); // weekly breach with m independent attackers
            setParamLine(n, m, p, q, R);

            const pmf = binomialPMF(n, q);

            // ===== SIZE LOCK: capture canvas CSS sizes once and keep them stable for the whole run =====
            {
                const r1 = pathsC.getBoundingClientRect();
                const r2 = histC.getBoundingClientRect();
                CANVAS_LOCK = {
                    paths: { w: Math.max(1, r1.width), h: Math.max(1, r1.height) },
                    hist: { w: Math.max(1, r2.width), h: Math.max(1, r2.height) }
                };
            }

            // Animated accumulation by batches: visually shows convergence of the empirical histogram to theory
            let acc = new Map();
            let drawn = [];
            let done = 0;
            try {
                while (done < R) {
                    if (myRun !== RUN_ID) break;
                    const chunk = Math.min(batch, R - done);
                    const { paths, endpoints } = simulate(n, chunk, q, Math.min(keep, 120)); // draw a subset per batch
                    // accumulate endpoints
                    for (const [s, c] of endpoints.entries())
                        acc.set(s, (acc.get(s) || 0) + c);
                    // maintain only last 'keep' paths
                    drawn = drawn.concat(paths).slice(-keep);

                    // redraw with current totals (sizes stay stable thanks to CANVAS_LOCK)
                    drawPaths(drawn, pathsC);
                    drawHistogram(acc, n, done + chunk, pmf, histC);
                    renderTable(acc, n, done + chunk, pmf);
                    setStatsLine(n, q, done + chunk, acc);
                    const tv = totalVariation(acc, n, done + chunk, pmf);
                    document.getElementById('statline').innerHTML +=
                        `Total variation distance = <strong>${tv.toFixed(5)}</strong> (after ${done + chunk})`;

                    // Save current state for resize-safe redraws
                    lastState = { paths: drawn, endpoints: new Map(acc), n, R: done + chunk, pmf, q };

                    // Short pause for animation
                    await new Promise(r => setTimeout(r, 70));
                    done += chunk;
                }
            } finally {
                // ===== UNLOCK at end (even if loop breaks/throws) =====
                CANVAS_LOCK = null;
            }
        });

        document.getElementById('clear').addEventListener('click', () => {
            RUN_ID++;
            document.getElementById('statscontainer').style.display = 'none';
            // Clear canvases
            const { cssW: w1, cssH: h1, ctx: c1 } = prepCanvas(pathsC, "paths");
            c1.clearRect(0, 0, w1, h1);
            const { cssW: w2, cssH: h2, ctx: c2 } = prepCanvas(histC, "hist");
            c2.clearRect(0, 0, w2, h2);
            // Clear text
            document.getElementById('tableWrap').textContent = 'Cleared.';
            document.getElementById('paramline').textContent = '';
            document.getElementById('statline').textContent = '';
            // Reset state
            lastState = { paths: [], endpoints: new Map(), n: 0, R: 0, pmf: new Float64Array(0), q: 0 };
            // Ensure no stale lock remains
            CANVAS_LOCK = null;
        });

        /* =========================
           Redraw on resize
           ========================= */
        /* On resize we redraw. If a run is active, prepCanvas uses the lock so the plot area stays stable. */
        window.addEventListener('resize', () => {
            const { paths, endpoints, n, R, pmf } = lastState || {};
            if (!paths) return;
            if (paths.length) drawPaths(paths, pathsC);
            if (pmf && pmf.length && n > 0) drawHistogram(endpoints, n, R, pmf, histC);
        });
    </script>
</div>

---

## **Code Screenshot**

![code](/assets/images/code_5.png)

---

## **Code Analysis**

Below are the key sections of the code, presented completely and explained line by line.

### **a. Probability Setup**

```js
const q = 1 - Math.pow(1 - p, m); // weekly breach probability from m independent attackers
setParamLine(n, m, p, q, R);
```

**Explanation**<br>
This converts the **per-attacker breach probability p** into the **weekly probability q** that at least one attacker succeeds.
It is later used both for generating the random walks and computing the theoretical Binomial distribution.

### **b. Simulation Core**

```js
function simulate(n, R, q, keep = 400) {
    const kept = Math.min(R, keep);
    const paths = new Array(kept);
    for (let i = 0; i < kept; i++) paths[i] = new Int16Array(n + 1);

    const endpoints = new Map(); // key = S_n ∈ {-n, -n+2, ..., n}

    for (let r = 0; r < R; r++) {
        let s = 0;
        const store = (r < kept);
        if (store) paths[r][0] = 0;

        for (let t = 1; t <= n; t++) {
            s += (Math.random() < q) ? -1 : 1; // breach → -1, secure → +1
            if (store) paths[r][t] = s;
        }
        endpoints.set(s, (endpoints.get(s) || 0) + 1);
    }

    return { paths: kept ? paths : [], endpoints };
}
```

**Explanation**<br>

- Simulates **R independent trajectories** each lasting n weeks.

- Each trajectory starts at 0 and adds ±1 depending on whether a breach occurs.

- Stores a subset of paths (up to “keep”) for visualization.

- Counts how many simulations end with each possible total S<sub>n</sub>.

### **c. Binomial Distribution**

```js
function binom(n, k) {
    if (k < 0 || k > n) return 0;
    if (k === 0 || k === n) return 1;
    k = Math.min(k, n - k);
    let c = 1;
    for (let i = 1; i <= k; i++) c = (c * (n - k + i)) / i;
    return c;
}

function binomialPMF(n, q) {
    const pmf = new Float64Array(n + 1);
    for (let k = 0; k <= n; k++)
        pmf[k] = binom(n, k) * Math.pow(q, k) * Math.pow(1 - q, n - k);
    return pmf;
}
```

**Explanation**<br>

- `binom(n,k)` computes the binomial coefficient using a stable multiplicative method.

- `binomialPMF` builds a full vector of theoretical probabilities P(K = k) for K ∼ Binomial(n,q).

- These probabilities are later mapped via S<sub>n</sub> = n − 2k for comparison with the simulated outcomes.

### **d. Trajectory Plot (Random Walk Visualization)**

```js
function drawPaths(paths, canvas) {
    const { cssW, cssH, ctx } = prepCanvas(canvas, "paths");
    ctx.clearRect(0, 0, cssW, cssH);
    if (!paths.length) return;

    const n = paths[0].length - 1;
    const padL = 60, padB = 34, padT = 14, padR = 16;
    const W = cssW - padL - padR;
    const H = cssH - padT - padB;
    const x0 = padL, y0 = cssH - padB;

    // Find global min/max for vertical scale
    let gmin = 0, gmax = 0;
    for (const p of paths) {
        for (let t = 0; t <= n; t++) {
            gmin = Math.min(gmin, p[t]);
            gmax = Math.max(gmax, p[t]);
        }
    }
    if (gmin === gmax) { gmin--; gmax++; }

    const sx = W / n;
    const sy = H / (gmax - gmin);

    // Axes
    ctx.strokeStyle = "#e2e8f0";
    ctx.lineWidth = 1;
    ctx.beginPath();
    ctx.moveTo(x0, y0);
    ctx.lineTo(x0 + W, y0);
    ctx.stroke();

    ctx.beginPath();
    ctx.moveTo(x0, y0 - H);
    ctx.lineTo(x0, y0);
    ctx.stroke();

    // Draw all paths
    ctx.save();
    ctx.beginPath();
    ctx.rect(x0, y0 - H, W, H);
    ctx.clip();

    const xMax = x0 + W;
    for (const p of paths) {
        const grad = ctx.createLinearGradient(x0, 0, x0 + W, 0);
        grad.addColorStop(0, 'white');
        grad.addColorStop(1, '#b5e853');
        ctx.strokeStyle = grad;
        ctx.lineWidth = 0.8;
        ctx.globalAlpha = 0.8;
        ctx.beginPath();
        for (let t = 0; t <= n; t++) {
            const x = Math.min(xMax - 0.5, x0 + t * sx);
            const y = y0 - (p[t] - gmin) * sy;
            if (t === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
        }
        ctx.stroke();
    }
    ctx.globalAlpha = 1;
    ctx.restore();
}
```

**Explanation**<br>

- Plots the simulated trajectories on the canvas.

- The gradient emphasizes direction (white → green).

- Scaling is automatic and clipped within plot boundaries.

- The axes correspond to time t and score S<sub>t</sub>.

### **e. Endpoint Histogram with Theoretical Curve**

```js
function drawHistogram(endpoints, n, R, pmf, canvas) {
    const { cssW, cssH, ctx } = prepCanvas(canvas, "hist");
    ctx.clearRect(0, 0, cssW, cssH);

    const padL = 60, padB = 34, padT = 14, padR = 16;
    const W = cssW - padL - padR;
    const H = cssH - padT - padB;
    const x0 = padL, y0 = cssH - padB;

    const scores = [];
    for (let s = -n; s <= n; s += 2) scores.push(s);

    const barW = Math.max(2, Math.min(26, W / (scores.length + 2)));
    const xOf = s => x0 + ((s + n) / 2 + 1) * barW;

    let maxF = 0;
    for (const s of scores) {
        const f = (endpoints.get(s) || 0) / R;
        if (f > maxF) maxF = f;
    }
    const yOfFreq = f => y0 - f * (H * 0.95 / maxF);

    // Draw bars
    ctx.fillStyle = "#ffffff";
    for (const s of scores) {
        const f = (endpoints.get(s) || 0) / R;
        const x = xOf(s), y = yOfFreq(f);
        ctx.fillRect(x - barW * 0.45, y, barW * 0.9, y0 - y);
    }

    // Overlay theoretical curve
    ctx.strokeStyle = "#b5e853";
    ctx.lineWidth = 2;
    ctx.beginPath();
    let first = true;
    for (let k = 0; k <= n; k++) {
        const s = n - 2 * k;
        const prob = pmf[k];
        const x = xOf(s), y = yOfFreq(prob);
        if (first) { ctx.moveTo(x, y); first = false; } else ctx.lineTo(x, y);
    }
    ctx.stroke();
}
```

**Explanation**<br>

- Computes normalized empirical frequencies of each S<sub>n</sub>.
​
- Draws them as vertical bars (white).
  
- Theoretical probabilities from Binomial(n, q) are plotted as a smooth green curve.

- The comparison shows how empirical results converge to the expected distribution.

### **f. Total Variation Distance**

```js
function totalVariation(endpoints, n, R, pmf) {
    let tv = 0;
    for (let k = 0; k <= n; k++) {
        const s = n - 2 * k;
        const f = (endpoints.get(s) || 0) / R;
        tv += Math.abs(f - pmf[k]);
    }
    return 0.5 * tv;
}
```

**Explanation**<br>

Calculates the total variation distance between the empirical and theoretical distributions.<br>
A lower value of this metric indicates that the observed simulation frequencies are closer to the Binomial law.

---

## **Results and Discussion**

As the number of weeks n and simulation runs R increase:

- The empirical mean and variance approach the theoretical values:

<div class="formula-box">
E[S<sub>n</sub>​] = n(1 − 2q), &nbsp;&nbsp;&nbsp; Var[S<sub>n</sub>​] = 4nq(1−q)
</div>

- The histogram becomes smoother and closely fits the Binomial curve.

- The total variation distance tends to zero, confirming convergence.

The random-walk visualization highlights how bias in q (the drift) affects the expected direction of S<sub>t</sub>.

---

# **Conclusion**

This experiment links **cybersecurity event modeling** with **probability theory**.
By simulating weekly outcomes as a biased random walk, we can visualize and quantify convergence toward the **Binomial distribution**.
The interactive web tool effectively demonstrates the relationship between **individual breach probabilities**, **collective attack risk**, and the **law of large numbers**.

---


