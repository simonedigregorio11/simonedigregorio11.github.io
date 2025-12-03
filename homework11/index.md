---
title: "Homework 11"
layout: default
permalink: /homework11/
---

<link rel="stylesheet" href="/assets/css/pages/homework11.css">

[← Back to Home](/)

---

# **Stochastic Differential Equation Simulator**

## **Introduction**

This project presents an interactive tool for the simulation of **Stochastic Differential Equations (SDEs)** using the **Euler–Maruyama method**.<br><br>
The simulator supports:

- The **Wiener process** 𝑊<sub>𝑡</sub>, representing **standard Brownian motion**.

- A general SDE

<div class="formula-box">
dX<sub>t</sub> = a(X<sub>t</sub>,t)dt + b(X<sub>t</sub>,t) dW<sub>t</sub>
</div>
where both the **drift** and **diffusion** are user-defined.

---

## **Theoretical Background**

### **1. The Wiener Process**

The **Wiener process**, also called **standard Brownian motion**, is the fundamental building block of modern stochastic calculus. It can be seen as the continuous-time limit of a symmetric random walk.

A **Wiener process** W<sub>t</sub> has:

**1.** W₀ = 0<br>
**2.** Independent increments<br>
**3.** Gaussian increments:

<div class="formula-box">
W<sub>t+h</sub> − W<sub>t</sub> ∼ N(0, h)
</div>

This means that, over any interval of length h, the increment of the Wiener process is normally distributed with mean 0 and variance h.

**4.** Continuous paths

It satisfies the SDE:

<div class="formula-box">
dX<sub>t</sub> = dW<sub>t</sub>
</div>

---

### **2. General Stochastic Differential Equations**

A general **SDE** is:

<div class="formula-box">
dX<sub>t</sub> = a(X<sub>t</sub>,t) dt + b(X<sub>t</sub>,t) dW<sub>t</sub>
</div>

Where:

- a(x,t) = **drift**: deterministic direction of motion

- b(x,t) = **diffusion**: magnitude of randomness

- dW<sub>t</sub> = infinitesimal increment of the Wiener process

SDEs model systems where uncertainty plays a structural role. Some classical examples include:

- **Ornstein–Uhlenbeck process** (mean reversion)

<div class="formula-box">
dX<sub>t</sub> = 𝜃(𝜇 − X<sub>t</sub>) dt + 𝜎 dW<sub>t</sub>	​
</div>

- **Geometric Brownian motion** (finance)

dX<sub>𝑡</sub> = 𝜇X<sub>t</sub> dt + 𝜎 X<sub>t</sub> dW<sub>t</sub>

- **Black–Scholes equation** for option pricing

These models illustrate how SDEs can encode growth, decay, oscillation, and noise simultaneously.

---

### **3. Euler–Maruyama Method**

Most SDEs do not admit closed-form solutions, making numerical approximation essential. The **Euler–Maruyama method** is the SDE analogue of the classical Euler method for ODEs.

Given a time discretization:

<div class="formula-box"> t<sub>n</sub> = n Δt,\quad Δt = T/N </div>

we approximate the Wiener increments.
In continuous time, dW<sub>t</sub> represents the infinitesimal change of the Wiener process.<br><br>
In discrete time this becomes:

<div class="formula-box">
ΔW<sub>n</sub> = (Δt)<sup>1/2</sup> Z<sub>n</sub>, &nbsp; Z<sub>n</sub> ∼ N(0,1).
</div>

This identity expresses the fact that a Wiener process has independent Gaussian increments with variance proportional to the length of the time step.

Substituting this into the SDE gives the Euler–Maruyama update:

<div class="formula-box"> X<sub>n+1</sub> = X<sub>n</sub> + a(X<sub>n</sub>, t<sub>n</sub>)\, Δt + b(X<sub>n</sub>, t<sub>n</sub>)\, ΔW<sub>n</sub>. </div>

Using the explicit expression for ΔW<sub>n</sub>, this becomes:

<div class="formula-box">
X<sub>n+1</sub> = X<sub>n</sub> + a(X<sub>n</sub>, t<sub>n</sub>) Δt + b(X<sub>n</sub>, t<sub>n</sub>)(Δt)<sup>1/2</sup> Z<sub>n</sub>
</div>


with Z<sub>n</sub> ∼ N(0,1).

The terms represent:

**Deterministic drift contribution**: 

<div class="formula-box">
a(X<sub>n</sub>, t<sub>n</sub>) Δt
</div>

**Stochastic diffusion contribution**: 

<div class="formula-box">
b(X<sub>n</sub>, t<sub>n</sub>) (Δt)<sup>1/2</sup> Z<sub>n</sub>
</div>

This method is simple but surprisingly effective for educational and exploratory purposes. More advanced schemes exist (Milstein, Runge–Kutta SDE schemes), but Euler–Maruyama provides the clearest conceptual link between theory and computation.

---

## **Demo**

<div class="hw11">
    <div class="card">
        <h1>Stochastic Differential Equation Simulator</h1>
        <div class="panel">
            <div class="row">
                <label for="mode">Mode</label>
                <select id="mode">
                    <option value="wiener">Wiener process: dX = dW</option>
                    <option value="general">General SDE: dX = a(X,t) dt + b(X,t) dW</option>
                </select>

                <label for="T">Final time T</label>
                <input id="T" type="number" min="0.1" step="0.1" value="1">

                <label for="steps">Time steps N</label>
                <input id="steps" type="number" min="10" step="10" value="500">

                <label for="paths">Number of paths</label>
                <input id="paths" type="number" min="1" max="50" value="5">

                <label for="x0">Initial value X₀</label>
                <input id="x0" type="number" step="0.1" value="0">
            </div>

            <div class="row general-only">
                <label for="drift">Drift a(x, t)</label>
                <input id="drift" type="text" value="0" placeholder="Example: -0.5 * x">

                <label for="diffusion">Diffusion b(x, t)</label>
                <input id="diffusion" type="text" value="1" placeholder="Example: 1 + 0.2 * t">
            </div>

            <div class="buttons">
                <button id="btn-simulate">Simulate</button>
                <button id="btn-reset" class="danger">Reset</button>
            </div>

            <div class="info">
                <p id="status"></p>
            </div>
        </div>

        <!-- NOTE: no width/height attributes here; size is controlled via CSS + JS -->
        <canvas id="sdeCanvas"></canvas>
    </div>

    <script>
        // Wrap everything to avoid leaking globals
        (function () {
            // === DOM elements ===
            const modeSel = document.getElementById('mode');
            const TInput = document.getElementById('T');
            const stepsInput = document.getElementById('steps');
            const pathsInput = document.getElementById('paths');
            const x0Input = document.getElementById('x0');
            const driftInput = document.getElementById('drift');
            const diffInput = document.getElementById('diffusion');
            const btnSim = document.getElementById('btn-simulate');
            const btnReset = document.getElementById('btn-reset');
            const statusEl = document.getElementById('status');

            const generalRow = document.querySelector('.row.general-only');

            const canvas = document.getElementById('sdeCanvas');
            const ctx = canvas.getContext('2d');

            // Store last simulation so we can redraw on resize
            let lastSimulation = null;

            // Default values
            const DEFAULTS = {
                mode: 'wiener',
                T: 1,
                N: 500,
                nPaths: 5,
                x0: 0,
                drift: '0',
                diffusion: '1'
            };

            // Show/hide info block based on status text
            function updateInfoVisibility() {
                const infoDiv = statusEl.parentElement;
                if (!infoDiv) return;

                if (statusEl.textContent.trim() === "") {
                    infoDiv.classList.remove("visible");
                } else {
                    infoDiv.classList.add("visible");
                }
            }

            function applyDefaults() {
                modeSel.value = DEFAULTS.mode;
                TInput.value = DEFAULTS.T;
                stepsInput.value = DEFAULTS.N;
                pathsInput.value = DEFAULTS.nPaths;
                x0Input.value = DEFAULTS.x0;
                driftInput.value = DEFAULTS.drift;
                diffInput.value = DEFAULTS.diffusion;
                updateModeUI();
            }

            // === Make canvas crisp on high DPI screens ===
            function resizeCanvas() {
                const dpr = window.devicePixelRatio || 1;
                const rect = canvas.getBoundingClientRect();

                // Internal resolution scaled by device pixel ratio
                canvas.width = rect.width * dpr;
                canvas.height = rect.height * dpr;
            }

            // === Utility: standard normal random variable (Box-Muller) ===
            function randn() {
                // Generate Z ~ N(0,1)
                let u = 0, v = 0;
                while (u === 0) u = Math.random();
                while (v === 0) v = Math.random();
                return Math.sqrt(-2 * Math.log(u)) * Math.cos(2 * Math.PI * v);
            }

            // === Core simulator using Euler–Maruyama ===
            function simulateSDE(options) {
                /*
                 * Simulate SDE:
                 *   dX_t = a(X_t, t) dt + b(X_t, t) dW_t
                 * with Euler–Maruyama:
                 *   X_{n+1} = X_n + a(X_n, t_n) dt + b(X_n, t_n) sqrt(dt) * Z_n
                 * where Z_n ~ N(0,1).
                 */
                const { T, N, nPaths, x0, a, b } = options;

                const dt = T / N;
                const sqrtDt = Math.sqrt(dt);

                // Precompute times
                const times = new Array(N + 1);
                for (let n = 0; n <= N; n++) {
                    times[n] = n * dt;
                }

                // Allocate paths: paths[i][n]
                const paths = new Array(nPaths);
                for (let i = 0; i < nPaths; i++) {
                    paths[i] = new Array(N + 1);
                    paths[i][0] = x0;
                }

                // Time-stepping
                for (let n = 0; n < N; n++) {
                    const t = times[n];
                    for (let i = 0; i < nPaths; i++) {
                        const x = paths[i][n];

                        const drift = a(x, t);      // a(X_n, t_n)
                        const diffusion = b(x, t);  // b(X_n, t_n)
                        const z = randn();          // Z_n ~ N(0,1)

                        // Euler–Maruyama update
                        const xNext = x + drift * dt + diffusion * sqrtDt * z;
                        paths[i][n + 1] = xNext;
                    }
                }

                return { times, paths };
            }

            // === Build drift/diffusion functions from text expressions ===
            function buildFunction(expr, name) {
                /*
                 * Build a(x, t) or b(x, t) from user input.
                 * The expression can use variables x, t and Math.* functions.
                 */
                try {
                    const fn = new Function('x', 't', `
                "use strict";
                return (${expr});
            `);
                    // Test a simple call to catch obvious runtime errors
                    fn(0, 0);
                    return fn;
                } catch (err) {
                    alert('Error in ' + name + ' expression: ' + err.message);
                    throw err;
                }
            }

            // === Draw only axes, no paths (for empty initial view / reset) ===
            function drawEmptyAxes() {
                const w = canvas.width;
                const h = canvas.height;

                ctx.clearRect(0, 0, w, h);

                const marginLeft = 50;
                const marginRight = 20;
                const marginTop = 20;
                const marginBottom = 40;

                const xMin = 0;
                const xMax = parseFloat(TInput.value) || DEFAULTS.T;
                const yMin = -1;
                const yMax = 1;

                function xToCanvas(t) {
                    return marginLeft + (t - xMin) / (xMax - xMin) * (w - marginLeft - marginRight);
                }

                function yToCanvas(x) {
                    return marginTop + (yMax - x) / (yMax - yMin) * (h - marginTop - marginBottom);
                }

                // Axes
                ctx.strokeStyle = '#ffffff';
                ctx.lineWidth = 1;

                const xAxisY = yToCanvas(0);

                // x-axis
                ctx.beginPath();
                ctx.moveTo(marginLeft, xAxisY);
                ctx.lineTo(w - marginRight, xAxisY);
                ctx.stroke();

                // y-axis
                ctx.beginPath();
                ctx.moveTo(marginLeft, marginTop);
                ctx.lineTo(marginLeft, h - marginBottom);
                ctx.stroke();

                // Labels
                ctx.fillStyle = '#ffffff';
                ctx.font = '12px system-ui';
                ctx.textAlign = 'center';
                ctx.fillText('time t', (w + marginLeft - marginRight) / 2, h - 10);

                ctx.save();
                ctx.translate(15, (h - marginBottom + marginTop) / 2);
                ctx.rotate(-Math.PI / 2);
                ctx.fillText('X(t)', 0, 0);
                ctx.restore();

                // Ticks
                ctx.fillStyle = '#ffffff';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'top';

                const xTicks = 5;
                for (let i = 0; i <= xTicks; i++) {
                    const t = xMin + (i / xTicks) * (xMax - xMin);
                    const cx = xToCanvas(t);
                    const cy = xAxisY;
                    ctx.beginPath();
                    ctx.moveTo(cx, cy - 3);
                    ctx.lineTo(cx, cy + 3);
                    ctx.stroke();
                    ctx.fillText(t.toFixed(2), cx, cy + 5);
                }

                ctx.textAlign = 'right';
                ctx.textBaseline = 'middle';
                const yTicks = 5;
                for (let i = 0; i <= yTicks; i++) {
                    const xVal = yMin + (i / yTicks) * (yMax - yMin);
                    const cy = yToCanvas(xVal);
                    ctx.beginPath();
                    ctx.moveTo(marginLeft - 3, cy);
                    ctx.lineTo(marginLeft + 3, cy);
                    ctx.stroke();
                    ctx.fillText(xVal.toFixed(2), marginLeft - 5, cy);
                }
            }

            // === Plotting ===
            function drawPaths(times, paths) {
                /*
                 * Draw all simulated paths on the canvas.
                 * x-axis: time t
                 * y-axis: X_t value
                 */
                const w = canvas.width;
                const h = canvas.height;

                // Clear canvas
                ctx.clearRect(0, 0, w, h);

                // Margins for axes
                const marginLeft = 50;
                const marginRight = 20;
                const marginTop = 20;
                const marginBottom = 40;

                // Find min and max among all paths to scale y-axis
                let minVal = Infinity;
                let maxVal = -Infinity;
                for (const path of paths) {
                    for (const x of path) {
                        if (!Number.isFinite(x)) continue;
                        if (x < minVal) minVal = x;
                        if (x > maxVal) maxVal = x;
                    }
                }

                if (!Number.isFinite(minVal) || !Number.isFinite(maxVal)) {
                    statusEl.textContent = 'Error: non-finite values in simulation.';
                    updateInfoVisibility();
                    return;
                }

                // Avoid zero range
                if (maxVal === minVal) {
                    const eps = Math.abs(maxVal) > 0 ? 0.1 * Math.abs(maxVal) : 1;
                    maxVal += eps;
                    minVal -= eps;
                }

                const xMin = times[0];
                const xMax = times[times.length - 1];

                // Coordinate transforms
                function xToCanvas(t) {
                    return marginLeft + (t - xMin) / (xMax - xMin) * (w - marginLeft - marginRight);
                }

                function yToCanvas(x) {
                    return marginTop + (maxVal - x) / (maxVal - minVal) * (h - marginTop - marginBottom);
                }

                // Draw axes
                ctx.strokeStyle = '#ffffff';
                ctx.lineWidth = 1;

                // x-axis (time)
                const xAxisY = yToCanvas(0); // may be outside canvas but it's ok
                ctx.beginPath();
                ctx.moveTo(marginLeft, xAxisY);
                ctx.lineTo(w - marginRight, xAxisY);
                ctx.stroke();

                // y-axis
                ctx.beginPath();
                ctx.moveTo(marginLeft, marginTop);
                ctx.lineTo(marginLeft, h - marginBottom);
                ctx.stroke();

                // Axis labels (simple)
                ctx.fillStyle = '#ffffff';
                ctx.font = '12px system-ui';
                ctx.textAlign = 'center';
                ctx.fillText('time t', (w + marginLeft - marginRight) / 2, h - 10);

                ctx.save();
                ctx.translate(15, (h - marginBottom + marginTop) / 2);
                ctx.rotate(-Math.PI / 2);
                ctx.fillText('X(t)', 0, 0);
                ctx.restore();

                // Tick marks (simple: 5 ticks on each axis)
                ctx.fillStyle = '#ffffff';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'top';

                const xTicks = 5;
                for (let i = 0; i <= xTicks; i++) {
                    const t = xMin + (i / xTicks) * (xMax - xMin);
                    const cx = xToCanvas(t);
                    const cy = xAxisY;
                    ctx.beginPath();
                    ctx.moveTo(cx, cy - 3);
                    ctx.lineTo(cx, cy + 3);
                    ctx.stroke();
                    ctx.fillText(t.toFixed(2), cx, cy + 5);
                }

                ctx.textAlign = 'right';
                ctx.textBaseline = 'middle';
                const yTicks = 5;
                for (let i = 0; i <= yTicks; i++) {
                    const xVal = minVal + (i / yTicks) * (maxVal - minVal);
                    const cy = yToCanvas(xVal);
                    ctx.beginPath();
                    ctx.moveTo(marginLeft - 3, cy);
                    ctx.lineTo(marginLeft + 3, cy);
                    ctx.stroke();
                    ctx.fillText(xVal.toFixed(2), marginLeft - 5, cy);
                }

                // Draw paths
                for (let p = 0; p < paths.length; p++) {
                    const path = paths[p];

                    // Slightly thicker for the first path
                    ctx.lineWidth = (p === 0) ? 2 : 1;
                    // Different hue per path (quick and dirty)
                    const hue = (p * 60) % 360;
                    ctx.strokeStyle = `hsl(${hue}, 70%, 60%)`;

                    ctx.beginPath();
                    for (let n = 0; n < times.length; n++) {
                        const cx = xToCanvas(times[n]);
                        const cy = yToCanvas(path[n]);
                        if (n === 0) {
                            ctx.moveTo(cx, cy);
                        } else {
                            ctx.lineTo(cx, cy);
                        }
                    }
                    ctx.stroke();
                }
            }

            // === Handle mode change (Wiener / general SDE) ===
            function updateModeUI() {
                const mode = modeSel.value;
                if (mode === 'wiener') {
                    // For Wiener process: a(x,t) = 0, b(x,t) = 1 (ignored inputs)
                    generalRow.classList.add('disabled');
                    driftInput.disabled = true;
                    diffInput.disabled = true;
                } else {
                    generalRow.classList.remove('disabled');
                    driftInput.disabled = false;
                    diffInput.disabled = false;
                }
            }

            modeSel.addEventListener('change', updateModeUI);

            // === Main click handler: run simulation and plot ===
            btnSim.addEventListener('click', () => {
                statusEl.textContent = '';
                updateInfoVisibility();

                const mode = modeSel.value;
                const T = parseFloat(TInput.value);
                const N = parseInt(stepsInput.value, 10);
                const nPaths = parseInt(pathsInput.value, 10);
                const x0 = parseFloat(x0Input.value);

                if (!(T > 0) || !(N >= 10) || !(nPaths >= 1)) {
                    statusEl.textContent = 'Please choose T > 0, N ≥ 10, and at least one path.';
                    updateInfoVisibility();
                    return;
                }

                let a, b;

                if (mode === 'wiener') {
                    // === Required part: Wiener process dX = dW ===
                    // This corresponds to a(x,t) = 0, b(x,t) = 1
                    a = (x, t) => 0;
                    b = (x, t) => 1;
                    statusEl.textContent = 'Simulating Wiener process: dX = dW with Euler–Maruyama.';
                    updateInfoVisibility();
                } else {
                    // === Optional extension: general SDE dX = a(X,t) dt + b(X,t) dW ===
                    const driftExpr = driftInput.value.trim();
                    const diffExpr = diffInput.value.trim();

                    try {
                        a = buildFunction(driftExpr || '0', 'drift a(x,t)');
                        b = buildFunction(diffExpr || '1', 'diffusion b(x,t)');
                        statusEl.textContent = 'Simulating general SDE with user-defined drift and diffusion.';
                        updateInfoVisibility();
                    } catch (err) {
                        // buildFunction already alerted the user
                        return;
                    }
                }

                const { times, paths } = simulateSDE({ T, N, nPaths, x0, a, b });
                lastSimulation = { times, paths };
                drawPaths(times, paths);
            });

            // === Reset handler ===
            btnReset.addEventListener('click', () => {
                statusEl.textContent = '';
                updateInfoVisibility();
                lastSimulation = null;
                applyDefaults();
                resizeCanvas();
                drawEmptyAxes();
            });

            // Initial setup: defaults, canvas size, empty axes (no simulation)
            applyDefaults();
            resizeCanvas();
            statusEl.textContent = '';
            updateInfoVisibility();
            drawEmptyAxes();

            // On window resize, keep it sharp and redraw
            window.addEventListener('resize', () => {
                resizeCanvas();
                if (lastSimulation) {
                    drawPaths(lastSimulation.times, lastSimulation.paths);
                } else {
                    drawEmptyAxes();
                }
            });
        })();
    </script>
</div>

---

# **Structure of the Code**

## **Code Screenshot**
![code](/assets/images/code_7.png)

### **1. The Euler-Maruyama Simulator**

This function implements the full numerical method:

```js
function simulateSDE(options) {
    const { T, N, nPaths, x0, a, b } = options;
    const dt = T / N;
    const sqrtDt = Math.sqrt(dt);

    const times = new Array(N + 1);
    for (let n = 0; n <= N; n++) times[n] = n * dt;

    const paths = new Array(nPaths);
    for (let i = 0; i < nPaths; i++) {
        paths[i] = new Array(N + 1);
        paths[i][0] = x0;
    }

    for (let n = 0; n < N; n++) {
        const t = times[n];
        for (let i = 0; i < nPaths; i++) {
            const x = paths[i][n];
            const drift = a(x, t);
            const diffusion = b(x, t);
            const z = randn();
            paths[i][n + 1] = x + drift * dt + diffusion * sqrtDt * z;
        }
    }
    return { times, paths };
}
```
**Explanation**<br>
This function directly implements the Euler–Maruyama method. For each time step, it evaluates the drift and diffusion functions at the current state, generates a Gaussian increment through the randn() function, and computes the next state. The procedure is repeated for each independent path, allowing many trajectories to be generated in parallel.

The structure of the function reveals the fundamental relationship between theory and computation: the numerical scheme mirrors the mathematical update rule, with dt and sqrt(dt) applying exactly as prescribed by the continuous-time model.

### **2. Parsing User-Defined Drift and Diffusion**

```js
function buildFunction(expr, name) {
    try {
        const fn = new Function('x', 't', `
            "use strict";
            return (${expr});
        `);
        fn(0, 0);
        return fn;
    } catch (err) {
        alert('Error in ' + name + ' expression: ' + err.message);
        throw err;
    }
}
```

**Explanation**<br>
This component allows the user to write mathematical expressions directly into the interface. The system transforms these expressions into JavaScript functions of the variables x and t. By testing the resulting function on simple inputs, the code detects syntax errors before the simulation begins.

This flexible approach encourages experimentation and allows the simulator to support a broad class of SDEs without requiring predefined functional forms.

### **3. Generating Standard Normal Random Variables**

```js
function randn() {
    let u = 0, v = 0;
    while (u === 0) u = Math.random();
    while (v === 0) v = Math.random();
    return Math.sqrt(-2 * Math.log(u)) * Math.cos(2 * Math.PI * v);
}
```

**Explanation**<br>
This implementation uses the **Box–Muller transform** to convert uniformly distributed random numbers into standard normal variables. These variables represent the increments Z_n used in the Euler–Maruyama update rule. The mathematical properties of Gaussian increments are essential to reproducing the behavior of the Wiener process, and therefore to simulating any SDE.

### **4. Rendering Sample Paths on the Canvas**

```js
function drawPaths(times, paths) {
    // Canvas setup omitted for brevity
    // Axes drawing, scaling, ticks...

    for (let p = 0; p < paths.length; p++) {
        const path = paths[p];
        ctx.lineWidth = (p === 0) ? 2 : 1;
        const hue = (p * 60) % 360;
        ctx.strokeStyle = `hsl(${hue}, 70%, 60%)`;

        ctx.beginPath();
        for (let n = 0; n < times.length; n++) {
            const cx = xToCanvas(times[n]);
            const cy = yToCanvas(path[n]);
            if (n === 0) ctx.moveTo(cx, cy);
            else ctx.lineTo(cx, cy);
        }
        ctx.stroke();
    }
}
```

**Explanation**<br>
This function handles the visualization of the simulated trajectories. It assigns a distinct color to each path to improve readability, computes the appropriate canvas coordinates for each point, and draws smooth lines connecting successive states.

The dynamic nature of the scaling functions xToCanvas and yToCanvas ensures that the displayed graph adapts automatically to the magnitude of the simulated values, whether they remain close to the origin or diverge widely due to strong drift or diffusion.

---

## **Conclusions**

This homework assignment provides a comprehensive introduction to **Stochastic Differential Equations** both in theory and in computational practice. By implementing the **Euler–Maruyama method** and integrating it into an interactive visualization tool, the student gains direct insight into the behavior of SDEs, their sensitivity to drift and diffusion, and the variability of sample paths.

The simulator demonstrates how numerical methods can approximate continuous-time stochastic models and highlights the importance of randomness in shaping the evolution of such systems. Through experimentation, one can observe mean reversion, instability, oscillation, and dispersion, developing a deeper intuition for the mathematical structures underlying stochastic dynamics.


---