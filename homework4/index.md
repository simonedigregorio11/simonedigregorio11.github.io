---
title: "Homework 4"
layout: default
permalink: /homework4/
---

<link rel="stylesheet" href="/assets/css/pages/homework4.css">

[← Back to Home](/)

---

# **Law of large numbers simulation of relative frequency trajectories**
# **Overview**

This document presents an interactive HTML + Canvas app that simulates multiple paths of the relative frequency of successes across Bernoulli trials and compares those paths to the theoretical expectation. It also displays the distribution of the final relative frequency f(N) via a compact horizontal histogram.

The goal is twofold:

- Provide an accessible, visual illustration of the **Law of Large Numbers** (LLN): as the number of trials N grows, the observed relative frequency f(t) tends to stabilize around the true success probability p.

- Offer a clean, well-structured codebase that separates **simulation** from **rendering**, and includes **UI** features (inputs, progress bar, responsive canvas) suitable for coursework or demonstration.

# **Introduction & Motivation**

The **Bernoulli** model is a canonical starting point for studying randomness. Each trial returns 1 (“success”) with probability p and 0 (“failure”) otherwise. If we run N trials and track the cumulative proportion of successes after t=1,2,…,N, we obtain a trajectory f(t)= 1/t ​∑<sup>t</sup><sub>i=1</sub> X<sub>i</sub> that should, on average, approach p.

Visualizing many such trajectories reveals two key phenomena:

- **High early volatility**: When t is small, each outcome moves f(t) substantially.
  
- Stabilization: As t grows, fluctuations shrink and trajectories cluster around p.

This app implements exactly that picture: multiple **trajectories**, a **reference line** at y=p, and a **histogram** of f(N).

## **Theoretical Background**

At the heart of this simulation lies a very simple random experiment: imagine flipping a coin many times. Each flip can either succeed (for example, “heads”) or fail (“tails”). The probability of success is called p.

If we repeat this experiment several times and keep track of how many successes we get, we can calculate the relative frequency of successes.

What we discover is that, although the sequence of results is unpredictable at the beginning, the relative frequency tends to stabilize around the true probability p as the number of trials grows. Early on, the curve jumps up and down; later, it becomes smoother and remains close to that constant value.

This visual behavior is a direct illustration of one of the most important ideas in probability theory: the Law of Large Numbers. It tells us that the more we repeat a random experiment, the closer our observed average becomes to the real underlying probability.

In this project:

- Each line on the canvas represents a different trajectory of the relative frequency across many trials.

- The dashed line marks the theoretical expectation, which is the target value the curves should approach.

- On the right, the histogram shows the final outcomes of all trajectories, giving a snapshot of how the frequencies are distributed at the end of the experiment.

## **Application Structure**

- UI Panel: text inputs for **N** (trials), **m** (paths), **p** (success prob, fixed here at 0.5), and **delay** (animation cadence); Run and Reset buttons.

- Canvas: left area for **trajectories**, right area for the **histogram** of f(N).

- Legend & Info: color swatches and a textual summary with the empirical mean of f(N).
  
- Progress Bar: updates while drawing batches, then hides on completion.

---

# **Law of Large Numbers Simulator**

<div class="hw4">
  <div class="row">
    <label>Trials (N)</label>
    <input id="N" type="number" min="1" max="2000" value="200" />
    <label>Paths (m)</label>
    <input id="paths" type="number" min="1" max="2000" value="200" />
    <label>p (success prob)</label>
    <input id="pprob" type="number" value="0.5" disabled />
    <label>Batch delay ms</label>
    <input id="delay" type="number" min="0" max="2000" value="30" />
  </div>

  <div class="buttons">
    <button id="run">Run</button>
    <button id="clear" class="danger">Reset</button>
  </div>

  <canvas id="plot"></canvas>

  <div class="legend">
    <div><span class="swatch" id="trajectories"></span> trajectories f(t)</div>
    <div><span class="swatch" id="theoretical-average"></span> theoretical average (y = p)</div>
    <div><span class="swatch" id="histogram"></span> histogram of f(N)</div>
  </div>

  <!-- progress bar -->
  <div id="progress" class="progress" aria-hidden="true">
    <div class="progress__track">
      <div class="progress__bar"></div>
    </div>
    <span class="progress__label">0%</span>
  </div>

  <div class="meta" id="info"></div>

  <script>
    /* -------------------- utilities -------------------- */
    const $ = sel => document.querySelector(sel);

    function randBernoulli(p) { return Math.random() < p ? 1 : 0; }

    function dpiScaleCanvas(canvas, targetCssH) {
      const ratio = window.devicePixelRatio || 1;
      const cssW = canvas.clientWidth || canvas.parentElement.clientWidth || 800;
      canvas.width = Math.floor(cssW * ratio);
      canvas.height = Math.floor(targetCssH * ratio);
      canvas.style.height = targetCssH + 'px';
      const ctx = canvas.getContext('2d');
      ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
      return ctx;
    }

    /* -------------------- layout helpers -------------------- */
    function layoutRects(canvas) {
      const totalW = canvas.clientWidth;
      const totalH = parseInt(getComputedStyle(canvas).height, 10);

      const gap = 18;
      const histW = Math.min(220, Math.floor(totalW * 0.28));
      const mainW = totalW - histW - gap;

      const main = { x: 0, y: 0, w: mainW, h: totalH, m: { left: 60, right: 12, top: 30, bottom: 60 } };
      const hist = { x: mainW + gap, y: 0, w: histW, h: totalH, m: { left: 12, right: 12, top: 25, bottom: 55 } };
      return { main, hist };
    }

    function mapper(main, N) {
      const { w, h, m } = main;
      const W = w - m.left - m.right;
      const H = h - m.top - m.bottom;
      return {
        x: t => m.left + W * (t / N),
        y: y => m.top + H * (1 - y)   // y in [0,1]
      };
    }

    /* -------------------- drawing axes -------------------- */
    function drawMainAxes(ctx, main, N) {
      const { w, h, m } = main;

      // grid
      ctx.save();
      ctx.strokeStyle = '#ffffff';
      ctx.lineWidth = 1;
      for (let i = 0; i <= 5; i++) {
        const yy = m.top + (h - m.top - m.bottom) * i / 5;
        ctx.beginPath(); ctx.moveTo(m.left, yy); ctx.lineTo(w - m.right, yy); ctx.stroke();
      }

      // axes
      ctx.strokeStyle = '#ffffff';
      ctx.lineWidth = 1.2;
      ctx.beginPath();
      ctx.moveTo(m.left, m.top); ctx.lineTo(m.left, h - m.bottom); ctx.lineTo(w - m.right, h - m.bottom);
      ctx.stroke();

      // labels
      ctx.fillStyle = '#ffffff';
      ctx.font = '12px system-ui';
      ctx.fillText('Trials →', w - m.right - 28, h - m.bottom + 38);
      ctx.fillText('Relative freq →', m.left - 47, m.top - 15);

      // y ticks 0..1
      for (let i = 0; i <= 5; i++) {
        const val = (1 - i / 5).toFixed(2);
        const yy = m.top + (h - m.top - m.bottom) * i / 5;
        ctx.fillText(val, m.left - 46, yy + 4);
      }

      // x ticks
      const xs = [0, Math.floor(N / 4), Math.floor(N / 2), Math.floor(3 * N / 4), N];
      for (const xi of xs) {
        const xx = m.left + (w - m.left - m.right) * (xi / N);
        ctx.fillText(String(xi), xx - 8, h - m.bottom + 18);
      }
      ctx.restore();
    }

    function drawHistFrame(ctx, hist) {
      const { x, w, h, m } = hist;
      ctx.save();
      ctx.strokeStyle = '#ffffff';
      ctx.lineWidth = 1;
      ctx.strokeRect(x + m.left - 1, m.top - 1, w - m.left - m.right + 2, h - m.top - m.bottom + 2);

      ctx.fillStyle = '#ffffff';
      ctx.font = '12px system-ui';
      ctx.fillText('Distribution of f(N)', x + 11, m.top - 9);
      ctx.restore();
    }

    /* -------------------- simulation -------------------- */
    function simulatePath(N, p) {
      const seq = new Float32Array(N + 1);
      seq[0] = 0;
      let acc = 0;
      for (let t = 1; t <= N; t++) {
        acc += randBernoulli(p);
        seq[t] = acc / t;
      }
      return seq;
    }

    /* -------------------- histogram -------------------- */
    function buildHistogram(values, bins) {
      const counts = new Array(bins).fill(0);
      for (const v of values) {
        const clamped = Math.max(0, Math.min(1, v));
        const b = Math.min(bins - 1, Math.floor(clamped * bins));
        counts[b]++;
      }
      return counts;
    }

    function drawHistogram(ctx, hist, counts, paths) {
      const { x, w, h, m } = hist;
      const innerX = x + m.left;
      const innerY = m.top;
      const innerW = w - m.left - m.right;
      const innerH = h - m.top - m.bottom;
      const bins = counts.length;
      const binH = innerH / bins;
      const maxCount = Math.max(...counts, 1);

      ctx.save();
      ctx.beginPath();
      ctx.rect(innerX, innerY, innerW, innerH);
      ctx.clip();
      const barColor = 'rgba(250,50,50,1)';
      ctx.fillStyle = barColor;

      for (let i = 0; i < bins; i++) {
        const frac = counts[i] / maxCount;
        const barW = 8 + Math.floor(frac * (innerW - 8));
        const yTop = m.top + i * binH;

        ctx.fillStyle = `rgba(${getComputedStyle(document.documentElement)
          .getPropertyValue('--red').trim()},0.9)`;
        ctx.fillRect(x + m.left, yTop + 1, barW, Math.max(1, binH - 2));
      }

      // y-scale tick labels (0.0 .. 1.0)
      ctx.fillStyle = '#ffffff';
      ctx.font = '11px system-ui';
      ctx.textAlign = 'right';
      const steps = 5;
      for (let k = 0; k <= steps; k++) {
        const yy = m.top + innerH * (k / steps);
        const val = (1 - k / steps).toFixed(1);
        ctx.fillText(val, x + w - 15, yy + 12);
      }
      ctx.restore();
    }

    /* -------------------- main render -------------------- */
    function drawReferenceLine(ctx, map, N, p) {
      ctx.save();
      ctx.strokeStyle = 'rgba(181, 232, 83, 1)';
      ctx.setLineDash([6, 4]);
      ctx.lineWidth = 2;
      ctx.beginPath();
      ctx.moveTo(map.x(0), map.y(p));
      ctx.lineTo(map.x(N), map.y(p));
      ctx.stroke();
      ctx.setLineDash([]);
      ctx.restore();
    }

    function drawPath(ctx, seq, map) {
      ctx.beginPath();
      ctx.strokeStyle = 'rgba(181, 232, 83, 0.12)';
      ctx.lineWidth = 1;
      ctx.moveTo(map.x(0), map.y(seq[0]));
      for (let t = 1; t < seq.length; t++) {
        ctx.lineTo(map.x(t), map.y(seq[t]));
      }
      ctx.stroke();
    }

    /* -------------------- PROGRESS helpers -------------------- */
    const uiProgress = {
      box: document.getElementById('progress'),
      bar: document.querySelector('#progress .progress__bar'),
      label: document.querySelector('#progress .progress__label')
    };
    function showProgress(show) {
      if (!uiProgress.box) return;
      uiProgress.box.style.display = show ? 'flex' : 'none';
      uiProgress.box.setAttribute('aria-hidden', show ? 'false' : 'true');
    }
    function setProgress(frac) {
      if (!uiProgress.bar || !uiProgress.label) return;
      const clamped = Math.max(0, Math.min(1, frac || 0));
      const pct = Math.round(clamped * 100);
      uiProgress.bar.style.width = pct + '%';
      uiProgress.label.textContent = pct + '%';
    }

    /* -------------------- controller -------------------- */
    const canvas = $('#plot');
    let ctx = dpiScaleCanvas(canvas, 520);

    function clearCanvas() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    function renderScaffold(N) {
      const { main, hist } = layoutRects(canvas);
      drawMainAxes(ctx, main, N);
      drawHistFrame(ctx, hist);
      return { main, hist };
    }

    async function runSimulation({ N, paths, p, delay }) {
      // prepare canvas
      ctx = dpiScaleCanvas(canvas, 520);
      clearCanvas();
      const { main, hist } = renderScaffold(N);
      const map = mapper(main, N);
      drawReferenceLine(ctx, map, N, p);

      // PROGRESS: start and reset bar
      showProgress(true);
      setProgress(0);

      const finals = new Array(paths);


      const batch = Math.max(1, Math.min(25, Math.ceil(paths / 40)));
      for (let i = 0; i < paths; i += batch) {
        const upto = Math.min(paths, i + batch);
        for (let j = i; j < upto; j++) {
          const seq = simulatePath(N, p);
          drawPath(ctx, seq, map);
          finals[j] = seq[N];
        }
        // PROGRESS: update after each batch
        setProgress(upto / paths);
        if (delay > 0) {
          await new Promise(r => setTimeout(r, delay));
        } else {
          await new Promise(requestAnimationFrame);
        }
      }


      // histogram of f(N)
      const bins = 40;
      const counts = buildHistogram(finals, bins);
      drawHistogram(ctx, hist, counts, paths);

      // info line
      const mean = finals.reduce((a, b) => a + b, 0) / paths;
      $('#info').textContent =
        `Paths: ${paths}, Trials N: ${N}, p: ${p.toFixed(3)} — empirical mean f(N): ${mean.toFixed(4)}.`;

      // PROGRESS: complete to 100%
      setProgress(1);

      return { finals, counts, bins };
    }

    /* -------------------- wire UI -------------------- */
    function readInt(id, lo, hi, fallback) {
      const v = parseInt($(id).value, 10);
      if (Number.isFinite(v)) return Math.max(lo, Math.min(hi, v));
      return fallback;
    }
    function readFloat(id, lo, hi, fallback) {
      const v = parseFloat($(id).value);
      if (Number.isFinite(v)) return Math.max(lo, Math.min(hi, v));
      return fallback;
    }

    $('#run').addEventListener('click', async () => {
      const N = readInt('#N', 1, 2000, 200);
      const paths = readInt('#paths', 1, 2000, 200);
      const p = readFloat('#pprob', 0, 1, 0.5);
      const delay = readInt('#delay', 0, 2000, 10);

      $('#info').textContent = 'Running simulation...';

      try {
        await runSimulation({ N, paths, p, delay });
      } finally {
        // PROGRESS: hide the bar at the end of the run
        showProgress(false);
      }
    });

    $('#clear').addEventListener('click', () => {
      ctx = dpiScaleCanvas(canvas, 520);
      clearCanvas();

      document.querySelectorAll('.hw4 input').forEach(el => {
        el.value = el.defaultValue;
      });

      // ← add this line to redraw the axes and histogram frame
      const N = readInt('#N', 1, 2000, 200);
      const { main, hist } = renderScaffold(N);

      $('#info').textContent = 'Canvas reset.';
      setProgress(0);
      showProgress(false);
    });


    // initial sizing & ready text
    function onResize() {
      ctx = dpiScaleCanvas(canvas, 520);
      clearCanvas();
      // keep axes visible with a default N for scale marks
      const { main, hist } = renderScaffold(readInt('#N', 1, 2000, 200));
      // leave panels empty otherwise
    }
    window.addEventListener('resize', onResize);
    $('#info').textContent = 'Ready. Set parameters and click Run.';
    onResize();

  </script>
</div>

---
<br>

## **Code Overview Screenshot**

![code](/assets/images/code_3.png)

## **Key Code Walkthrough**
### **1. Bernoulli Random Generator**

```js
function randBernoulli(p) { 
  return Math.random() < p ? 1 : 0; 
}
```

- Generates a single random variable that can be either 1 (success) or 0 (failure).
  
- Each call represents one Bernoulli trial with probability p of success.
  
- The function is the building block of the entire experiment — it defines the randomness.
  
- It assumes every trial is independent and identically distributed (i.i.d.).
  
- Theoretically, it models the simplest binary random process in probability.


### **2. Relative Frequency Sequence**

```js
function simulatePath(N, p) {
  const seq = new Float32Array(N + 1);
  seq[0] = 0;
  let acc = 0;
  for (let t = 1; t <= N; t++) {
    acc += randBernoulli(p); 
    seq[t] = acc / t;        
  }
  return seq;
}
```
- Runs the experiment for N trials.
  
- Uses ```randBernoulli(p)``` to simulate each random outcome.

- Keeps track of the cumulative number of successes ```(acc)```.

- Computes the relative frequency f(t) = successes/t at each step.
  
- Returns an array of values showing how f(t) evolves over time.
  
- Core theoretical meaning: it directly represents the sample mean process.
  
- Demonstrates the Law of Large Numbers — f(t) stabilizes around the true probability p as t increases.

### **3. Collecting the Final Results and Empirical Mean**

```js
const finals = new Array(paths);
for (let j = 0; j < paths; j++) {
  const seq = simulatePath(N, p);
  finals[j] = seq[N];
}

const mean = finals.reduce((a, b) => a + b, 0) / paths;
```

- Repeats the experiment for many independent paths (replications).

- Stores the final relative frequency f(N) of each trajectory.

- Computes the empirical mean across all paths.

- The empirical mean approximates the true probability p.

- Conceptual link: shows the consistency of the estimator — with many repetitions, the observed average converges to p.

- The set of ```finals``` represents the distribution of sample means for finite N.


### **4. Empirical Distribution of Final Frequencies**

```js
function buildHistogram(values, bins) {
  const counts = new Array(bins).fill(0);
  for (const v of values) {
    const clamped = Math.max(0, Math.min(1, v));
    const b = Math.min(bins - 1, Math.floor(clamped * bins));
    counts[b]++;
  }
  return counts;
}
```
- Transforms the list of final relative frequencies f(N) into grouped counts.
  
- Divides the interval [0, 1] into a fixed number of bins.
  
- Counts how many simulated outcomes fall into each bin.

- The result describes the empirical distribution of f(N).

- Theoretical meaning:
  - Each f(N) corresponds to Binomial(N,p)/N.

  - As N increases, the distribution concentrates around p.

  - The variance decreases as p(1 − p)/N, so the standard deviation scales as 1/√N — this shows how fluctuations shrink with more trials.
  
  - For large N, f(N) is approximately Normal with mean p and variance p(1 − p)/N.

### **5. Reading Input Parameters**

```js
function readInt(id, lo, hi, fallback) {
  const v = parseInt($(id).value, 10);
  if (Number.isFinite(v)) return Math.max(lo, Math.min(hi, v));
  return fallback;
}

function readFloat(id, lo, hi, fallback) {
  const v = parseFloat($(id).value);
  if (Number.isFinite(v)) return Math.max(lo, Math.min(hi, v));
  return fallback;
}
```
- Validates and sanitizes numerical inputs from the user interface.

- Ensures N, p, and other parameters stay within valid bounds.

- Prevents unrealistic or invalid experimental configurations.

- While not directly theoretical, these helpers guarantee experimental consistency.

- Indirectly, they allow meaningful comparisons between different runs.

## **Using the Application**

1. Set **Trials (N)** and **Paths (m)**. Larger N → tighter clustering around p; larger m → smoother histogram.
  
2. p is fixed at 0.5 here (editable in code if needed).
   
3. Choose **Batch delay** to control animation pacing.
   
4. Click Run to simulate and draw; Reset to clear and redraw axes/frame.

Tip: Try N = 50 vs N = 1000 with the same m. Observe how f(t) stabilizes and how the histogram narrows.

## **Output Interpretation**

- Trajectories f(t): Many faint green lines. Early swings are large; later they dampen—a visual of **variance decay** p(1−p)/t.

- Reference line y=p: Dashed green line—your theoretical target.

- Histogram of f(N): Right panel. For large N, the bars concentrate near p. For small N, the distribution is wider and visibly asymmetric for p ≠ 0.5.

# **Conclusion**

This simulation provides a clear, intuitive view of the Law of Large Numbers.
By repeating simple Bernoulli trials and tracking the relative frequency of success, it shows how random outcomes gradually stabilize around the true probability p.

The code transforms a theoretical idea into a concrete experiment: the average of many random trials becomes predictable when the number of repetitions is large.
In essence, it turns abstract probability into visible evidence that order emerges from randomness.

---