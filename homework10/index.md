---
title: "Homework 10"
layout: default
permalink: /homework10/
---

<link rel="stylesheet" href="/assets/css/pages/homework10.css">

[← Back to Home](/)

---

# **Simulation of a Counting Process With Rate λ**

## **Introduction**

In this assignment we study the simulation of a **counting process** on the interval **[0,1]**, where events occur independently and with a constant average rate **λ**. Following the professor’s instructions, the continuous-time model is approximated through a discrete construction: the interval is divided into **n** small subintervals and each one produces an event with probability **λ/n**. In this way we obtain a sequence of independent Bernoulli random variables whose sum mimics the behaviour of a Poisson process.<br>

The goal of the project is twofold. First, we want to simulate a single trajectory of the counting process N(t) to visualize its step-wise evolution. Second, we simulate many replications of N(1) in order to examine the empirical distribution of the total number of events over the unit interval, and compare it with the theoretical **Poisson(λ)** distribution. The implementation uses HTML, CSS and JavaScript, together with HTML5 canvas for drawing the path and the histogram.

---

## **Theoretical Background**

A **Poisson process** with rate **λ** is defined by three fundamental properties: it starts from zero, it has independent increments, and its increments are stationary, meaning that the number of events occurring in any interval of length t follows a Poisson distribution with mean λt. In particular, for the unit interval we have N(1) ∼ Poisson(λ), implying the well-known identities **E[N(1)] = λ** and **Var(N(1)] = λ**.<br>

Since simulating an actual continuous-time Poisson process can be more laborious, we use a classical approximation: divide the time horizon into **n** equal subintervals of length 1/n, and simulate independent Bernoulli random variables

<div class="formula-box">
X<sub>i</sub> ​∼ Bernoulli(p),&nbsp;&nbsp;p = λ​/n.
</div>

The cumulative sum N<sub>n</sub>(t) then approximates a Poisson process. As **n** becomes large, this discrete construction converges in distribution to the continuous-time model, a result sometimes called the Poisson limit theorem or the “law of rare events”.

---
# **Demo**

<div class="hw10">
    <div class="card">
        <h2>Counting Process Simulation</h2>
        <div class="panel">
            <div class="row">
                <label for="lambda">Rate λ (expected events on [0,1])</label>
                <input id="lambda" type="number" min="0" step="0.5" value="16" />

                <label for="n">Subintervals n</label>
                <input id="n" type="number" min="10" max="50000" value="5000" />

                <label for="R">Replications R (for N(1) distribution)</label>
                <input id="R" type="number" min="1" max="5000" value="500" />
            </div>

            <div class="buttons">
                <button id="btn-one">Simulate one path</button>
                <button id="btn-many" class="secondary">Simulate R paths (N(1) distribution)</button>
                <button id="btn-reset" class="danger">Reset</button>
            </div>
        </div>

        <div class="canvases">
            <div class="canvas-card">
                <div class="canvas-title">Counting path of N(t)</div>
                <canvas id="pathCanvas"></canvas>
                <div class="stats" id="pathStats">
                    Click “Simulate one path” to generate a sample trajectory.
                </div>
            </div>

            <div class="canvas-card">
                <div class="canvas-title">Empirical distribution of N(1)</div>
                <canvas id="histCanvas"></canvas>
                <div class="stats" id="histStats">
                    Click “Simulate R paths” to estimate the distribution of the total count N(1).
                </div>
            </div>
        </div>

    </div>

    <script>
        (function () {
            const lambdaInput = document.getElementById("lambda");
            const nInput = document.getElementById("n");
            const RInput = document.getElementById("R");
            const btnOne = document.getElementById("btn-one");
            const btnMany = document.getElementById("btn-many");
            const btnReset = document.getElementById("btn-reset");

            const pathCanvas = document.getElementById("pathCanvas");
            const histCanvas = document.getElementById("histCanvas");
            const pathStats = document.getElementById("pathStats");
            const histStats = document.getElementById("histStats");

            let lastPath = null;
            let lastHist = null;

            // Prepare canvas for HiDPI displays and return 2D context
            function setupCanvas(canvas) {
                const dpr = window.devicePixelRatio || 1;
                const rect = canvas.getBoundingClientRect();
                canvas.width = rect.width * dpr;
                canvas.height = rect.height * dpr;
                const ctx = canvas.getContext("2d");
                ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
                return ctx;
            }

            // Simulate a single counting path N_n(t) on [0,1] using Bernoulli(λ/n) increments
            function simulateOnePath(lambda, n) {
                const p = Math.min(lambda / n, 1); // λ/n, capped at 1
                let count = 0;
                const points = [{ t: 0, c: 0 }];
                for (let i = 0; i < n; i++) {
                    if (Math.random() < p) {
                        count++;
                    }
                    const t = (i + 1) / n;
                    points.push({ t, c: count });
                }
                return points;
            }

            // Draw step path of N(t) with axes, ticks and labels
            function drawPath(points) {
                const ctx = setupCanvas(pathCanvas);
                const w = pathCanvas.clientWidth;
                const h = pathCanvas.clientHeight;

                ctx.clearRect(0, 0, w, h);
                ctx.fillStyle = "#000000";
                ctx.fillRect(0, 0, w, h);

                const margin = 30;
                const x0 = margin + 15;
                const y0 = h - margin;
                const x1 = w - margin + 20;
                const y1 = margin;

                // AXES
                ctx.strokeStyle = "#ffffff";
                ctx.lineWidth = 1;
                ctx.beginPath();
                ctx.moveTo(x0, y0);
                ctx.lineTo(x1, y0); // x-axis
                ctx.stroke();

                ctx.beginPath();
                ctx.moveTo(x0, y0);
                ctx.lineTo(x0, y1); // y-axis
                ctx.stroke();

                // AXIS LABELS
                ctx.fillStyle = "#ffffff";
                ctx.font = "12px system-ui";

                // X-axis label
                ctx.fillText("t (time)", (x0 + x1) / 2 - 15, y0 + 28);

                // Y-axis label
                ctx.save();
                ctx.translate(x0 - 28, (y0 + y1) / 2 + 20);
                ctx.rotate(-Math.PI / 2);
                ctx.fillText("N(t)", 0, 0);
                ctx.restore();

                // TICKS: X-axis
                ctx.fillStyle = "#ffffff";
                ctx.font = "10px system-ui";
                const xticks = [0, 0.25, 0.5, 0.75, 1];
                xticks.forEach(t => {
                    const x = x0 + t * (x1 - x0);
                    ctx.beginPath();
                    ctx.moveTo(x, y0 - 3);
                    ctx.lineTo(x, y0 + 3);
                    ctx.stroke();
                    ctx.fillText(t.toString(), x - 6, y0 + 14);
                });

                // Compute maxC for Y-axis ticks
                let maxC = 5; // default if no data
                if (points && points.length) {
                    maxC = 0;
                    for (const p of points) if (p.c > maxC) maxC = p.c;
                    if (maxC === 0) maxC = 1;
                } else {
                    const lambdaVal = parseFloat(lambdaInput.value);
                    if (isFinite(lambdaVal) && lambdaVal > 0) {
                        maxC = Math.max(1, Math.round(2 * lambdaVal));
                    }
                }

                // TICKS: Y-axis
                const yticks = [0, maxC / 4, maxC / 2, (3 * maxC) / 4, maxC];
                yticks.forEach(v => {
                    const y = y0 - (v / maxC) * (y0 - y1);
                    ctx.beginPath();
                    ctx.moveTo(x0 - 3, y);
                    ctx.lineTo(x0 + 3, y);
                    ctx.stroke();
                    ctx.fillText(Math.round(v), x0 - 18, y + 3);
                });

                // If no path is available, stop after drawing axes and ticks
                if (!points || !points.length) return;

                // STEP PLOT
                ctx.strokeStyle = "#b5e853";
                ctx.lineWidth = 1.5;
                ctx.beginPath();

                let first = true;
                for (let i = 0; i < points.length; i++) {
                    const { t, c } = points[i];
                    const x = x0 + t * (x1 - x0);
                    const y = y0 - (c / maxC) * (y0 - y1);

                    if (first) {
                        ctx.moveTo(x0, y0);
                        ctx.lineTo(x, y0);
                        first = false;
                    } else {
                        const prev = points[i - 1];
                        const xPrev = x0 + prev.t * (x1 - x0);
                        const yPrev = y0 - (prev.c / maxC) * (y0 - y1);
                        ctx.lineTo(x, yPrev);
                        if (c !== prev.c) ctx.lineTo(x, y);
                    }
                }

                ctx.stroke();
            }

            // Simulate R independent samples of N(1) via Bernoulli(λ/n) sums
            function simulateN1Distribution(lambda, n, R) {
                const p = Math.min(lambda / n, 1);
                const counts = [];
                for (let r = 0; r < R; r++) {
                    let c = 0;
                    for (let i = 0; i < n; i++) {
                        if (Math.random() < p) c++;
                    }
                    counts.push(c);
                }
                return counts;
            }

            // Draw histogram (or default axes) for the empirical distribution of N(1)
            function drawHistogram(counts) {
                const ctx = setupCanvas(histCanvas);
                const w = histCanvas.clientWidth;
                const h = histCanvas.clientHeight;

                ctx.clearRect(0, 0, w, h);
                ctx.fillStyle = "#000000";
                ctx.fillRect(0, 0, w, h);

                const margin = 30;
                const x0 = margin + 15;
                const y0 = h - margin;
                const x1 = w - margin + 20;
                const y1 = margin;

                // AXES
                ctx.strokeStyle = "#ffffff";
                ctx.lineWidth = 1;

                ctx.beginPath();
                ctx.moveTo(x0, y0);
                ctx.lineTo(x1, y0); // x-axis
                ctx.stroke();

                ctx.beginPath();
                ctx.moveTo(x0, y0);
                ctx.lineTo(x0, y1); // y-axis
                ctx.stroke();

                // AXIS LABELS
                ctx.fillStyle = "#ffffff";
                ctx.font = "12px system-ui";

                ctx.fillText("k (count value)", (x0 + x1) / 2 - 35, y0 + 28);

                ctx.save();
                ctx.translate(x0 - 28, (y0 + y1) / 2 + 35);
                ctx.rotate(-Math.PI / 2);
                ctx.fillText("Frequency", 0, 0);
                ctx.restore();

                // Decide whether we have real data or not
                const hasData = counts && counts.length;

                let keys = [];
                let freq = new Map();
                let maxFreq = 1; // positive default to avoid division by zero

                if (hasData) {
                    // Build frequency map from simulated counts
                    for (const c of counts) {
                        freq.set(c, (freq.get(c) || 0) + 1);
                    }
                    keys = Array.from(freq.keys()).sort((a, b) => a - b);
                    maxFreq = Math.max(...keys.map(k => freq.get(k)));
                    if (maxFreq <= 0) maxFreq = 1;
                } else {
                    // No data yet: build a default range of k values
                    const lambdaVal = parseFloat(lambdaInput.value);
                    const maxK = (isFinite(lambdaVal) && lambdaVal > 0)
                        ? Math.max(3, Math.round(2 * lambdaVal))
                        : 5;
                    for (let k = 0; k <= maxK; k++) keys.push(k);
                    // Frequencies are not drawn in this case, but we keep maxFreq = 1
                }

                ctx.fillStyle = "#ffffff";
                ctx.font = "10px system-ui";

                // Y-axis ticks based on maxFreq (real or default)
                const yticks = [0, maxFreq / 2, maxFreq];
                yticks.forEach(v => {
                    const y = y0 - (v / maxFreq) * (y0 - y1);
                    ctx.beginPath();
                    ctx.moveTo(x0 - 3, y);
                    ctx.lineTo(x0 + 3, y);
                    ctx.stroke();
                    ctx.fillText(Math.round(v), x0 - 18, y + 3);
                });

                // Limit how many x-axis labels we draw to avoid overlapping
                const barWidth = (x1 - x0) / keys.length;
                const maxXTicks = 15; // maximum number of x labels
                const showEvery = Math.max(1, Math.ceil(keys.length / maxXTicks));

                // X-axis ticks for integer k values
                keys.forEach((k, idx) => {
                    const x = x0 + idx * barWidth + barWidth / 2;

                    // Draw small tick for every bar
                    ctx.beginPath();
                    ctx.moveTo(x, y0 - 3);
                    ctx.lineTo(x, y0 + 3);
                    ctx.stroke();

                    // Draw label only for some ticks
                    if (idx % showEvery === 0) {
                        ctx.fillText(String(k), x - 4, y0 + 14);
                    }
                });

                // If there is no data yet, we stop here: only axes and ticks are shown
                if (!hasData) return;

                // BARS: draw the empirical histogram when data is available
                ctx.fillStyle = "#b5e853";
                keys.forEach((k, idx) => {
                    const f = freq.get(k);
                    const x = x0 + idx * barWidth + 2;
                    const barH = (f / maxFreq) * (y0 - y1);
                    const y = y0 - barH;
                    ctx.fillRect(x, y, barWidth - 4, barH);
                });
            }

            // Update textual stats for a single path
            function updatePathStats(points, lambda, n) {
                const finalCount = points[points.length - 1].c;
                pathStats.innerHTML =
                    `Final count on [0,1]: <code>N(1) = ${finalCount}</code><br>` +
                    `<div class="stats-grid">
                        <div>Theoretical mean: <code>E[N(1)] = λ = ${lambda.toFixed(3)}</code></div>
                        <div>Theoretical variance: <code>Var[N(1)] = λ = ${lambda.toFixed(3)}</code></div>
                        <div>Construction: <code>N<sub>n</sub>(1) = Σ X<sub>i</sub></code>, <code>X<sub>i</sub> ~ Bernoulli(λ/n)</code></div>
                    </div>`;
            }

            // Update textual stats for the empirical N(1) distribution
            function updateHistStats(counts, lambda) {
                const R = counts.length;
                let sum = 0;
                for (const c of counts) sum += c;
                const mean = sum / R;
                let varSum = 0;
                for (const c of counts) {
                    const d = c - mean;
                    varSum += d * d;
                }
                const variance = varSum / (R - 1 || 1);

                histStats.innerHTML =
                    `<div class="stats-grid">
                        <div>Replications: <code>R = ${R}</code></div>
                        <div>Empirical mean: <code>${mean.toFixed(3)}</code></div>
                        <div>Empirical variance: <code>${variance.toFixed(3)}</code></div>
                        <div>Theoretical mean/variance: <code>λ = ${lambda.toFixed(3)}</code></div>
                    </div>`;
            }

            // Event: simulate one path
            btnOne.addEventListener("click", () => {
                let lambda = parseFloat(lambdaInput.value);
                let n = parseInt(nInput.value, 10);
                if (!isFinite(lambda) || lambda < 0) lambda = 0;
                if (!Number.isInteger(n) || n < 10) n = 10;

                const path = simulateOnePath(lambda, n);
                lastPath = { lambda, n, path };
                drawPath(path);
                updatePathStats(path, lambda, n);
            });

            // Event: simulate many paths and draw histogram
            btnMany.addEventListener("click", () => {
                let lambda = parseFloat(lambdaInput.value);
                let n = parseInt(nInput.value, 10);
                let R = parseInt(RInput.value, 10);
                if (!isFinite(lambda) || lambda < 0) lambda = 0;
                if (!Number.isInteger(n) || n < 10) n = 10;
                if (!Number.isInteger(R) || R < 1) R = 1;

                const counts = simulateN1Distribution(lambda, n, R);
                lastHist = { lambda, n, R, counts };
                drawHistogram(counts);
                updateHistStats(counts, lambda);
            });

            // Reset both plots, text, and input values
            btnReset.addEventListener("click", () => {
                lastPath = null;
                lastHist = null;

                // Reset input fields to default values
                lambdaInput.value = 16;
                nInput.value = 5000;
                RInput.value = 500;

                pathStats.innerHTML = 'Click “Simulate one path” to generate a sample trajectory.';
                histStats.innerHTML = 'Click “Simulate R paths” to estimate the distribution of the total count N(1).';

                // Draw empty axes with default ticks
                drawPath(null);
                drawHistogram(null);
            });

            // Redraw on window resize to keep canvases sharp and aligned
            window.addEventListener("resize", () => {
                if (lastPath) {
                    drawPath(lastPath.path);
                } else {
                    drawPath(null);
                }
                if (lastHist) {
                    drawHistogram(lastHist.counts);
                } else {
                    drawHistogram(null);
                }
            });

            // On page load: draw empty axes (with ticks) for both canvases
            drawPath(null);
            drawHistogram(null);
        })();
    </script>
</div>


---

# **Structure of the Code**

The provided code creates an interactive tool entirely in the browser. It includes input fields for **λ**, **n** and **R**, buttons to run the simulations, and two canvases: one for the sample path and one for the empirical distribution of N(1).<br>

## **Code Screenshot**

![code](/assets/images/code_6.png)

### **1. Simulation of One Path**

```js
function simulateOnePath(lambda, n) {
    const p = Math.min(lambda / n, 1); // λ/n, capped at 1
    let count = 0;
    const points = [{ t: 0, c: 0 }];
    for (let i = 0; i < n; i++) {
        if (Math.random() < p) {
            count++;
        }
        const t = (i + 1) / n;
        points.push({ t, c: count });
    }
    return points;
}
```

**Explanation**<br>
This function creates the full trajectory of the approximated counting process.
The probability of an event in each subinterval is computed as λ/n. A loop runs n times, and for each iteration a Bernoulli trial determines whether an event occurs. The variable **count** records the total number of events so far, and at every step the current time and cumulative count are stored in the **points** array. The output is a list of (t,N(t)) pairs that form the complete sample path of the process.

### **2. Drawing the Sample Path**

 ```js
 function drawPath(points) {
    const ctx = setupCanvas(pathCanvas);
    const w = pathCanvas.clientWidth;
    const h = pathCanvas.clientHeight;

    ctx.clearRect(0, 0, w, h);
    ctx.fillStyle = "#000000";
    ctx.fillRect(0, 0, w, h);

    const margin = 30;
    const x0 = margin + 15;
    const y0 = h - margin;
    const x1 = w - margin + 20;
    const y1 = margin;

    // AXES
    ctx.strokeStyle = "#ffffff";
    ctx.lineWidth = 1;
    ctx.beginPath();
    ctx.moveTo(x0, y0);
    ctx.lineTo(x1, y0); // x-axis
    ctx.stroke();

    ctx.beginPath();
    ctx.moveTo(x0, y0);
    ctx.lineTo(x0, y1); // y-axis
    ctx.stroke();

    // AXIS LABELS
    ctx.fillStyle = "#ffffff";
    ctx.font = "12px system-ui";

    // X-axis label
    ctx.fillText("t (time)", (x0 + x1) / 2 - 15, y0 + 28);

    // Y-axis label (rotated)
    ctx.save();
    ctx.translate(x0 - 28, (y0 + y1) / 2 + 20);
    ctx.rotate(-Math.PI / 2);
    ctx.fillText("N(t)", 0, 0);
    ctx.restore();

    // TICKS: X-axis
    ctx.fillStyle = "#ffffff";
    ctx.font = "10px system-ui";
    const xticks = [0, 0.25, 0.5, 0.75, 1];
    xticks.forEach(t => {
        const x = x0 + t * (x1 - x0);
        ctx.beginPath();
        ctx.moveTo(x, y0 - 3);
        ctx.lineTo(x, y0 + 3);
        ctx.stroke();
        ctx.fillText(t.toString(), x - 6, y0 + 14);
    });

    // Compute maxC for Y-axis ticks
    let maxC = 5; 
    if (points && points.length) {
        maxC = 0;
        for (const p of points) if (p.c > maxC) maxC = p.c;
        if (maxC === 0) maxC = 1;
    } else {
        const lambdaVal = parseFloat(lambdaInput.value);
        if (isFinite(lambdaVal) && lambdaVal > 0) {
            maxC = Math.max(1, Math.round(2 * lambdaVal));
        }
    }

    // TICKS: Y-axis
    const yticks = [0, maxC / 4, maxC / 2, (3 * maxC) / 4, maxC];
    yticks.forEach(v => {
        const y = y0 - (v / maxC) * (y0 - y1);
        ctx.beginPath();
        ctx.moveTo(x0 - 3, y);
        ctx.lineTo(x0 + 3, y);
        ctx.stroke();
        ctx.fillText(Math.round(v), x0 - 22, y + 3);
    });

    if (!points || !points.length) return;

    // STEP PLOT
    ctx.strokeStyle = "#b5e853";
    ctx.lineWidth = 1.5;
    ctx.beginPath();

    let first = true;
    for (let i = 0; i < points.length; i++) {
        const { t, c } = points[i];
        const x = x0 + t * (x1 - x0);
        const y = y0 - (c / maxC) * (y0 - y1);

        if (first) {
            ctx.moveTo(x0, y0);
            ctx.lineTo(x, y0);
            first = false;
        } else {
            const prev = points[i - 1];
            const xPrev = x0 + prev.t * (x1 - x0);
            const yPrev = y0 - (prev.c / maxC) * (y0 - y1);
            ctx.lineTo(x, yPrev);
            if (c !== prev.c) ctx.lineTo(x, y);
        }
    }

    ctx.stroke();
}
```
**Explanation**<br>
This function is responsible for visualising **the trajectory of the counting process**. It sets up the canvas, draws the axes, determines appropriate scaling, places tick marks and labels, and finally plots the characteristic step graph of the counting process. The key idea is to convert each time-value pair returned by ```simulateOnePath``` into a horizontal–vertical step, resulting in a staircase that closely resembles the path of a true Poisson process. Vertical jumps appear whenever the event count increases, while horizontal segments indicate periods without events.

### **3. Simulation of N(1) Distribution**

```js
function simulateN1Distribution(lambda, n, R) {
    const p = Math.min(lambda / n, 1);
    const counts = [];
    for (let r = 0; r < R; r++) {
        let c = 0;
        for (let i = 0; i < n; i++) {
            if (Math.random() < p) c++;
        }
        counts.push(c);
    }
    return counts;
}
```
**Explanation**<br>
This function repeats the **Bernoulli-based** approximation R times. Each repetition simulates one full trajectory on 
[0,1], but instead of storing all intermediate values, only the final count N(1) is kept. The resulting array of counts forms an **empirical approximation** of the distribution of N(1). Because each replication is independent, this is a straightforward Monte-Carlo estimator of the Poisson distribution.

### **4. Drawing the Histogram**

```js
function drawHistogram(counts) {
    const ctx = setupCanvas(histCanvas);
    const w = histCanvas.clientWidth;
    const h = histCanvas.clientHeight;

    ctx.clearRect(0, 0, w, h);
    ctx.fillStyle = "#000000";
    ctx.fillRect(0, 0, w, h);

    const margin = 30;
    const x0 = margin + 15;
    const y0 = h - margin;
    const x1 = w - margin + 20;
    const y1 = margin;

    // AXES
    ctx.strokeStyle = "#ffffff";
    ctx.lineWidth = 1;

    ctx.beginPath();
    ctx.moveTo(x0, y0);
    ctx.lineTo(x1, y0); // x-axis
    ctx.stroke();

    ctx.beginPath();
    ctx.moveTo(x0, y0);
    ctx.lineTo(x0, y1); // y-axis
    ctx.stroke();

    // AXIS LABELS
    ctx.fillStyle = "#ffffff";
    ctx.font = "12px system-ui";

    ctx.fillText("k (count value)", (x0 + x1) / 2 - 35, y0 + 28);

    ctx.save();
    ctx.translate(x0 - 28, (y0 + y1) / 2 + 35);
    ctx.rotate(-Math.PI / 2);
    ctx.fillText("Frequency", 0, 0);
    ctx.restore();

    if (!counts || !counts.length) return;

    const freq = new Map();
    for (const c of counts) {
        freq.set(c, (freq.get(c) || 0) + 1);
    }

    const keys = Array.from(freq.keys()).sort((a, b) => a - b);
    const maxFreq = Math.max(...keys.map(k => freq.get(k)));

    ctx.fillStyle = "#ffffff";
    ctx.font = "10px system-ui";

    // Y-axis ticks
    const yticks = [0, maxFreq / 2, maxFreq];
    yticks.forEach(v => {
        const y = y0 - (v / maxFreq) * (y0 - y1);
        ctx.beginPath();
        ctx.moveTo(x0 - 3, y);
        ctx.lineTo(x0 + 3, y);
        ctx.stroke();
        ctx.fillText(Math.round(v), x0 - 26, y + 3);
    });

    // X-axis ticks (integer k values)
    keys.forEach((k, idx) => {
        const barWidth = (x1 - x0) / keys.length;
        const x = x0 + idx * barWidth + barWidth / 2;

        ctx.beginPath();
        ctx.moveTo(x, y0 - 3);
        ctx.lineTo(x, y0 + 3);
        ctx.stroke();

        ctx.fillText(String(k), x - 4, y0 + 14);
    });

    // BARS
    const barWidth = (x1 - x0) / keys.length;
    ctx.fillStyle = "#b5e853";

    keys.forEach((k, idx) => {
        const f = freq.get(k);
        const x = x0 + idx * barWidth + 2;
        const barH = (f / maxFreq) * (y0 - y1);
        const y = y0 - barH;
        ctx.fillRect(x, y, barWidth - 4, barH);
    });
}
```

**Explanation**<br>
This function computes the **empirical frequencies** of the values returned by ```simulateN1Distribution```, determines a suitable vertical scale, draws axes and tick marks, and then constructs a bar for each integer **k** observed. The bar heights reflect the **empirical frequencies** of the simulated counts. Because this function adapts automatically to the range of observed values, it works correctly for any parameter combination entered by the user.

---

## **Interpretation of the Results**
When a single trajectory is simulated and displayed, the staircase shape of the path clearly shows the behaviour of a counting process: it is a right-continuous, non-decreasing function with jumps of size one. For larger values of **n**, the steps become more numerous and more evenly spaced, and the resulting trajectory resembles the idealised Poisson process.<br>

The distribution simulation confirms the expected theoretical behaviour. The histogram typically centres around **λ**, reflecting the fact that the mean of a Poisson random variable is equal to its parameter. The variance of the simulated values also approaches **λ**, as predicted by the theory. When the number of replications **R** increases, the empirical distribution becomes more stable and matches more closely the true Poisson shape.

---

## **Conclusion**

This project demonstrates how a continuous-time stochastic model, such as a **Poisson process**, can be approximated numerically through a sequence of independent Bernoulli trials. The code provides simulations of both individual trajectories and the distribution of N(1), and the graphical interface illustrates these ideas clearly. Overall, the results align with the theoretical model: the mean and variance of the simulated N(1) converge to **λ**, and the sample paths display the correct qualitative structure of a counting process.

---