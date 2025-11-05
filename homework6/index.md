---
title: "Homework 6"
layout: default
permalink: /homework6/
---

<link rel="stylesheet" href="/assets/css/pages/homework6.css">

[← Back to Home](/)

---

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
      <div class="statline"><span>Mean</span><span>${format4(stats.mean)}</span></div>
      <div class="statline"><span>Variance</span><span>${format4(stats.varianceSample)}</span></div>
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

![code](/assets/images/code_4.png)

# **Conclusion**

**Measures of location** are fundamental tools in statistics because they translate complex data into a single
interpretable number.<br>
Yet, the concept of **“average”** is **context-dependent**.<br>
The **arithmetic mean** is powerful but **sensitive to outliers**; the **median** is **robust** but less responsive to
small variations; the **mode** reveals **popularity** rather than balance.<br>
The **geometric** and **harmonic means** adapt to **multiplicative** or **rate-based processes**, while **weighted** and
**trimmed means** refine the analysis when data are **heterogeneous** or **contain anomalies**.<br>

Ultimately, **no single measure is universally superior**.<br>
A wise analyst first **examines the data distribution**, identifies its **characteristics**, and then selects the
**measure** that most accurately represents the **central tendency** relevant to the research question.<br>
Properly chosen, the **measure of location** not only summarizes the dataset but also **preserves its underlying meaning
and structure**, ensuring that statistical conclusions remain both **valid** and **insightful**.


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
    <strong>O(1)</strong>—constant and independent of the sample size.
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