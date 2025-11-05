---
title: "Homework 6"
layout: default
permalink: /homework6/
---

<link rel="stylesheet" href="/assets/css/pages/homework6.css">

[← Back to Home](/)

---

# **Online Computation of Mean and Variance**

# **Introduction**

In statistics, measures such as the **mean** and **variance** are fundamental descriptors of a dataset’s central
tendency and dispersion.
Beyond their theoretical importance, these quantities are also central to real-world data analysis, where observations
often arrive **sequentially** rather than all at once.

Traditional or “batch” methods compute the mean and variance using cumulative sums over the entire dataset.
While conceptually simple, such approaches require storing all past observations and performing large arithmetic
operations, which makes them **memory-intensive** and **numerically unstable**—especially when handling long data
streams or large-scale computations.

To overcome these limitations, **online (incremental) algorithms** update statistics as each new observation arrives.
Instead of recomputing everything from scratch, they apply compact **recurrence relationships** that keep only a few
running variables.
Among these, **Welford’s method** is one of the most elegant and stable formulations, providing accurate results even in
the presence of rounding errors.

This assignment has two main goals:

**1.** To **derive analytically** the recurrence formulas for the arithmetic mean and variance;

**2.** To **implement and test** an **online algorithm** that updates these statistics incrementally as new data become
available.

An interactive demo is also provided to visualize how the mean and variance evolve in real time, illustrating the
connection between mathematical derivation and computational implementation.

## **Analytical Derivations: Recurrences for Mean and Variance**

Let x<sub>1</sub>, x<sub>2</sub>, ..., x<sub>n</sub> be the observations.
Define the running mean

<div class="formula-box">
    μ<sub>k</sub> = (x<sub>1</sub> + ... + x<sub>k</sub>) / k.
</div>

Also define

<div class="formula-box">
    M2<sub>k</sub> = Σ<sub>i=1..k</sub>(x<sub>i</sub> − μ<sub>k</sub>)<sup>2</sup>,
</div>

the sum of squared deviations from the current mean.

### **1. Recurrence for the arithmetic mean**

<div class="formula-box">
    μ<sub>n</sub> = (x<sub>1</sub> + ... + x<sub>n−1</sub> + x<sub>n</sub>) / n
</div>

Since **x<sub>1</sub> + ... + x<sub>n−1</sub> = (n−1)·μ<sub>n−1</sub>**, we obtain:

<div class="formula-box">
    μ<sub>n</sub> = [(n−1)·μ<sub>n−1</sub> + x<sub>n</sub>] / n = μ<sub>n−1</sub> + (x<sub>n</sub> − μ<sub>n−1</sub>) /
    n
</div>

**Interpretation:**
the new mean equals the old mean plus 1/n of the innovation (x<sub>n</sub> − μ<sub>n−1</sub>).

### **2. Recurrence for variance via Welford’s M2**

Let:
<div class="formula-box">
    δ = x<sub>n</sub> − μ<sub>n−1</sub>
</div>

and update:

<div class="formula-box">
    μ<sub>n</sub> = μ<sub>n−1</sub> + δ / n.
</div>

Let:

<div class="formula-box">
    δ′ = x<sub>n</sub> − μ<sub>n</sub> (note δ′ = δ·(1 − 1/n)).
</div>

Then:

<div class="formula-box">
    M2<sub>n</sub> = M2<sub>n−1</sub> + δ · δ′
</div>

**Proof sketch:**

<div class="formula-box">

    M2<sub>n</sub> = Σ<sub>i=1..n</sub>(x<sub>i</sub> − μ<sub>n</sub>)<sup>2</sup>
    = Σ<sub>i=1..n−1</sub>(x<sub>i</sub> − μ<sub>n</sub>)<sup>2</sup> + (x<sub>n</sub> − μ<sub>n</sub>)<sup>2</sup>
</div>

Using:

<div class="formula-box">
    (x<sub>i</sub> − μ<sub>n</sub>) = (x<sub>i</sub> − μ<sub>n−1</sub>) − (μ<sub>n</sub> − μ<sub>n−1</sub>)
</div>

and μ<sub>n</sub> − μ<sub>n−1</sub> = δ / n, the cross term vanishes because:

<div class="formula-box">
    Σ<sub>i=1..n−1</sub>(x<sub>i</sub> − μ<sub>n−1</sub>) = 0.
</div>

Simplifying yields:

<div class="formula-box">
    δ · (x<sub>n</sub> − μ<sub>n</sub>) = δ · δ′.
</div>

<div style="text-align:right; margin-right: 150px">
    □
</div>

### **3. Sample and population variance from M2**

Population variance:

<div class="formula-box">
    σ<sup>2</sup><sub>n</sub> = M2<sub>n</sub> / n
</div>

Sample variance (unbiased):

<div class="formula-box">
    s<sup>2</sup><sub>n</sub> = M2<sub>n</sub> / (n − 1)
</div>

**Meaning of x<sub>n</sub>:** x<sub>n</sub> denotes the n-th observation (the newly arrived data point).<br><br><br>

We purposefully avoid the batch formula

<div class="formula-box">
    Var = (Σx² − (Σx)²/n)/(n−1)
</div>

due to catastrophic cancellation in floating-point arithmetic.

# **Mean and Variance Calculator Demo**

<div class="hw6">
    <div class="panel">
        <div class="tabs" role="tablist" aria-label="Input mode">
            <button id="tab-bulk" class="tab-btn" role="tab" aria-selected="true" aria-pressed="true">Paste Full
                Vector</button>
            <button id="tab-step" class="tab-btn" role="tab" aria-selected="false" aria-pressed="false">Add One by
                One</button>
        </div>

        <div class="grid">
            <!-- LEFT: Inputs -->
            <section>
                <!-- BULK INPUT -->
                <form id="pane-bulk">
                    <div class="row">
                        <label for="bulk-area">Numbers (comma / space / newline separated)</label>
                        <textarea id="bulk-area" rows="4" placeholder="e.g. 1, 2, 3, 4, 5"></textarea>
                    </div>
                    <div class="buttons">
                        <button type="button" id="btn-calc">Calculate</button>
                        <button type="button" id="btn-clear-bulk">Clear</button>
                    </div>
                </form>

                <!-- STEP INPUT -->
                <form id="pane-step" class="hidden" autocomplete="off">
                    <div class="row">
                        <label for="step-value">Next value</label>
                        <input id="step-value" type="number" step="any" placeholder="Enter a number" />
                    </div>
                    <div class="buttons">
                        <button type="button" id="btn-add">Add value</button>
                        <button type="button" id="btn-reset">Reset</button>
                    </div>
                    <div class="row card">
                        <div class="muted">Current data</div>
                        <div id="live-data" class="mono data-line">[]</div>
                    </div>
                </form>
            </section>

            <!-- RIGHT: Results -->
            <aside>
                <div class="card">
                    <div class="muted">Results</div>
                    <div id="results" class="mono" aria-live="polite">
                        <div class="muted">No data yet.</div>
                    </div>
                </div>
            </aside>
        </div>

        <div class="foot">
            Uses sample variance (denominator <span class="pill">n − 1</span>). Values formatted to 4 decimals.
        </div>
    </div>


    <script>
        // ---------- Online stats (Welford) ----------
        class OnlineStats {
            constructor() { this.reset(); }
            reset() {
                this.n = 0;        // count
                this.mean = 0;     // running mean
                this.M2 = 0;       // sum of squared diffs from the mean
            }
            add(x) {
                this.n += 1;
                const delta = x - this.mean;
                this.mean += delta / this.n;
                const delta2 = x - this.mean;
                this.M2 += delta * delta2;
            }
            get count() { return this.n; }
            get varianceSample() {
                return (this.n >= 2) ? this.M2 / (this.n - 1) : NaN;
            }
            get variancePopulation() {
                return (this.n >= 1) ? this.M2 / this.n : NaN;
            }
        }

        // ---------- State ----------
        const state = {
            data: [],                 // step mode: values to display
            stats: new OnlineStats(), // step mode: live stats
            lastDisplay: []           // last dataset shown in Results
        };

        // ---------- Helpers ----------
        const byId = (id) => document.getElementById(id);

        // Parse free-form numbers (commas / spaces / semicolons / newlines)
        function parseNumbers(text) {
            if (!text) return [];
            return text
                .split(/[\s,;]+/g)
                .map(s => parseFloat(s))
                .filter(n => Number.isFinite(n));
        }

        function format4(x) {
            return Number.isFinite(x) ? x.toFixed(4) : 'N/A';
        }

        function renderResults(displayArr, stats) {
            const el = byId('results');
            if (!stats || stats.count === 0) {
                el.innerHTML = `<div class="muted">No data yet.</div>`;
                return;
            }
            el.innerHTML = `
    <div class="statline"><span>Data (n)</span><span>${stats.count}</span></div>
    <div class="statline"><span>Values</span><span class="mono">[${displayArr.join(', ')}]</span></div>
    <div class="statline"><span>Mean (μ)</span><span>${format4(stats.mean)}</span></div>
    <div class="statline"><span>Sample variance (s²)</span><span>${format4(stats.varianceSample)}</span></div>
    <div class="statline"><span class="muted">Population variance (σ²)</span><span class="muted">${format4(stats.variancePopulation)}</span></div>
  `;
        }


        function setTab(mode /* 'bulk' | 'step' */) {
            const bulkTab = byId('tab-bulk');
            const stepTab = byId('tab-step');
            const bulkPane = byId('pane-bulk');
            const stepPane = byId('pane-step');

            const isBulk = mode === 'bulk';
            bulkPane.classList.toggle('hidden', !isBulk);
            stepPane.classList.toggle('hidden', isBulk);

            bulkTab.setAttribute('aria-pressed', String(isBulk));
            stepTab.setAttribute('aria-pressed', String(!isBulk));
            bulkTab.setAttribute('aria-selected', String(isBulk));
            stepTab.setAttribute('aria-selected', String(!isBulk));
        }

        // ---------- Event wiring ----------
        function init() {
            // Tabs
            byId('tab-bulk').addEventListener('click', () => setTab('bulk'));
            byId('tab-step').addEventListener('click', () => setTab('step'));

            // Bulk: feed values sequentially into OnlineStats
            byId('btn-calc').addEventListener('click', () => {
                const arr = parseNumbers(byId('bulk-area').value);
                if (!arr.length) {
                    alert('Please provide at least one valid number.');
                    return;
                }
                const stats = new OnlineStats();
                for (const x of arr) stats.add(x);
                state.lastDisplay = arr.slice();
                renderResults(state.lastDisplay, stats);
            });

            byId('btn-clear-bulk').addEventListener('click', () => {
                byId('bulk-area').value = '';
                state.lastDisplay = [];
                renderResults([], null);
            });

            // Step: maintain a single OnlineStats instance incrementally
            byId('btn-add').addEventListener('click', () => {
                const raw = byId('step-value').value.trim();
                const num = parseFloat(raw);
                if (!Number.isFinite(num)) {
                    alert('Please enter a valid number.');
                    return;
                }
                state.data.push(num);
                state.stats.add(num);
                byId('step-value').value = '';
                byId('live-data').textContent = `[${state.data.join(', ')}]`;
                state.lastDisplay = state.data.slice();
                renderResults(state.lastDisplay, state.stats);
                byId('step-value').focus();
            });

            // Enter to add quickly
            byId('step-value').addEventListener('keydown', (e) => {
                if (e.key === 'Enter') {
                    e.preventDefault();
                    byId('btn-add').click();
                }
            });

            byId('btn-reset').addEventListener('click', () => {
                state.data = [];
                state.stats.reset();
                state.lastDisplay = [];
                byId('live-data').textContent = '[]';
                renderResults([], null);
                byId('step-value').value = '';
                byId('step-value').focus();
            });

            // Default view: bulk
            setTab('bulk');
            renderResults([], null);
        }

        // Boot
        init();
    </script>
</div>
<br>

## **Code of the demo and explanation**

The implementation consists of a compact JavaScript module that demonstrates the **online computation of mean and
variance** through **Welford’s recurrence relations**.<br>
The code is organized into logically separated blocks for clarity, modularity, and maintainability.<br>

![code](/assets/images/code_4.png)

### **1. OnlineStats Class — Core Algorithm**

```js
class OnlineStats {
constructor() { this.reset(); }
reset() { this.n = 0; this.mean = 0; this.M2 = 0; }
add(x) { ... } // incremental update
get varianceSample() { ... }
get variancePopulation() { ... }
}
```

- Purpose: encapsulates the online (incremental) update logic.

- Variables maintained:

- n: number of observations.

- mean: running mean <sub>μ</sub>.

- M2: cumulative sum of squared deviations ∑(x<sub>i</sub> − μ<sub>n</sub>)<sup>2</sup>.

- Key formulae implemented:

<div class="formula-box">
    μ<sub>n</sub> = μ<sub>n−1</sub> + (x<sub>n</sub>-μ<sub>n-1</sub>) / n , M2<sub>n</sub> = M2<sub>n−1</sub> +
    (x<sub>n</sub> - μ<sub>n-1</sub>)(x<sub>n</sub> - μ<sub>n</sub>)
</div>

- Advantages:

- No need to store previous data (constant O(1) space).

- Resistant to catastrophic cancellation and floating-point overflow.

### **2. State Management**

```js
const state = {
data: [],
stats: new OnlineStats(),
lastDisplay: []
};
```

- Centralizes the live data, current statistics, and last computed vector.

- Enables **two input modes** (“Paste Full Vector” vs. “Add One by One”) to share the same computational backend.

### **3. Input Parsing and Validation**

```js
function parseNumbers(text) {
if (!text) return [];
return text.split(/[\s,;]+/g)
.map(s => parseFloat(s))
.filter(n => Number.isFinite(n));
}
```

- Accepts flexible delimiters (commas, spaces, newlines, semicolons).

- Filters out invalid or non-numeric inputs.

- Keeps the interface robust against formatting inconsistencies.<br>

(A localized version can optionally normalize commas as decimal points, e.g. “3,14 → 3.14”.)

### **4. Rendering of Results**

```js
function renderResults(displayArr, stats) { ... }
```
- Dynamically updates the HTML view each time new data are processed.

- Displays:

- sample size n,

- full data vector,

- mean (μ),

- sample variance (s²) and population variance (σ²).

- Values are formatted to four decimals for readability.<br>

This function connects the mathematical computation with an **interactive visual output**, reinforcing conceptual
understanding.

### **5. Event Wiring and Interaction Logic**

```js
function init() {
// tab selection, bulk mode, step-by-step mode, reset handlers
...
init();
}
```

- Manages user interaction across both input modes.

- “Paste Full Vector”: parses and processes all numbers at once.

- “Add One by One”: updates statistics in real time as new data arrive.

- Includes keyboard shortcuts (Enter to add quickly) and ARIA roles for accessibility.

This architecture mimics how streaming data are processed in modern analytics systems.

### **6. User Interface Components**

- Tabs toggle between batch (bulk) and streaming (step) input modes.

- Result panel shows the current summary statistics.

- Footer note reminds that the variance computed is the sample variance (n−1).

The HTML and CSS structure ensure semantic clarity and responsiveness while focusing on the statistical content rather
than layout aesthetics.

### **7. Summary**
The code exemplifies how theoretical recurrences for the **mean and variance** can be implemented in a concise, stable,
and efficient form.<br>
By combining mathematical rigor with clean software design, the demo provides both an analytical and computational
understanding of **online statistics** — bridging the gap between formula derivation and real-time application.

## **Quick Implementation Tests**

Use the **Paste Full Vector** tab and verify that the results match the expected values below
(using the sample variance with denominator n − 1).

<table class="table_2-box">
    <thead>
        <tr>
            <th>Data</th>
            <th>n</th>
            <th>Mean</th>
            <th>Sample Variance (s<sup>2</sup>)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>[2]</td>
            <td>1</td>
            <td>2.0000</td>
            <td>N/A</td>
        </tr>
        <tr>
            <td>[1, 2]</td>
            <td>2</td>
            <td>1.5000</td>
            <td>0.5000</td>
        </tr>
        <tr>
            <td>[1, 2, 3, 4]</td>
            <td>4</td>
            <td>2.5000</td>
            <td>1.6667</td>
        </tr>
        <tr>
            <td>[10, 10, 10]</td>
            <td>3</td>
            <td>10.0000</td>
            <td>0.0000</td>
        </tr>
        <tr>
            <td>[−1, 0, 1]</td>
            <td>3</td>
            <td>0.0000</td>
            <td>1.0000</td>
        </tr>
    </tbody>
</table>

Repeat the same in **Add One by One** mode and confirm identical final results.

# **Conclusion**
This demo shows how the mean and variance can be updated online with **O(1)** memory and time per datum.
Compared to batch formulas, **Welford’s** recurrences avoid catastrophic cancellation and reduce error propagation,
while remaining simple and fast for real-time or streaming data.
In short: online estimators are the practical default when data arrive sequentially or are too large to store.

# **Numerical Stability and Computational Advantages of Online Algorithms**

Traditional batch algorithms for computing the mean and variance require storing all observations and performing
operations on large cumulative sums.<br>
While conceptually simple, these methods are **numerically unstable** and **computationally inefficient**, especially
when processing long data streams or very large datasets.<br>

By contrast, **online algorithms**—such as **Welford’s method** implemented above—update the statistics
**incrementally** as each new observation arrives.

This approach offers several key advantages:

- **Numerical stability**
<div class="text-box">
    Online algorithms avoid catastrophic cancellation, which occurs in batch variance formulas involving the subtraction
    of two large, nearly equal numbers (e.g., Σx² − (Σx)²/n).
    <strong>Welford’s</strong> recurrence relations maintain the mean and the sum of squared deviations directly,
    minimizing floating-point round-off errors.
</div>

- **Memory efficiency**
<div class="text-box">
    Only a few scalar variables (n, mean, M2) are maintained, so the memory requirement is
    <strong>O(1)</strong>, constant and independent of the sample size.
    Batch methods, in contrast, require <strong>O(n)</strong> memory to store all data.
</div>

- **Computational efficiency**
<div class="text-box">
    Each update step has constant time complexity <strong>O(1)</strong>.
    This makes online algorithms suitable for <strong>real-time analysis</strong>, <strong>data streams</strong>, or
    <strong>embedded systems</strong> where data cannot be stored entirely in memory.
</div>

- **Error propagation control**
<div class="text-box">
    Since intermediate values remain bounded and updated progressively, errors accumulate slowly, improving robustness
    and <strong>long-term accuracy</strong>.
</div>

- **Scalability**
<div class="text-box">
    The same logic can be extended to parallel or <strong>distributed systems</strong>: partial statistics (count, mean,
    M2) from
    separate data blocks can be combined without reprocessing the raw data.
</div>

In summary, online algorithms are preferred for modern data analysis pipelines because they combine numerical precision,
computational efficiency, and robust scalability—qualities that traditional batch algorithms often lack.

---