---
title: "Homework 2"
layout: default
permalink: /homework2/
---

<link rel="stylesheet" href="/assets/css/pages/homework2.css">

[← Back to Home](/)

---

# **Datasets and Distributions**

# **Introduction**

In statistical science and data analysis, two fundamental concepts underpin almost every empirical and theoretical study: the **dataset** and the **distribution**.
While a dataset represents the organized collection of observed data, a distribution expresses the probabilistic or empirical structure underlying that data.
Understanding these concepts is essential for correctly designing experiments, modeling phenomena, and interpreting results.

## **The Concept of a Dataset**

A dataset is a structured collection of data, typically organized in a tabular or hierarchical format, representing measurements or observations about one or more **variables**.

Formally, a dataset D can be defined as:

<div class="formula-box">
D = {(x<sub>1</sub>, x<sub>2</sub>​,..., x<sub>n</sub>​) ∣ x<sub>i</sub> ​∈ X<sub>i</sub>}
</div>

where each x<sub>i</sub> represents the observed value of variable X<sub>i</sub> for a given instance or unit of observation.

## **Structure and Organization**

Datasets are often represented as **tables (matrices)**, where:

- Rows correspond to **observations** or instances (e.g., individual people, experiments, time points).

- Columns correspond to **variables** or features (e.g., age, height, temperature).

An example of a simple dataset:

<div class= "table-box">
<table>
  <thead>
    <tr>
      <th scope="col">ID</th>
      <th scope="col">Age</th>
      <th scope="col">Height (cm)</th>
      <th scope="col">Weight (kg)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td><td class="num">23</td><td class="num">175</td><td class="num">68</td>
    </tr>
    <tr>
      <td>2</td><td class="num">30</td><td class="num">182</td><td class="num">80</td>
    </tr>
    <tr>
      <td>3</td><td class="num">27</td><td class="num">169</td><td class="num">65</td>
    </tr>
  </tbody>
</table>
</div>

## **Classification of Datasets**

Datasets can be classified according to different criteria, depending on their structure and the type of information they contain.
Two of the most common distinctions are based on structure and measurement type.

### **1. Based on Structure**

This classification refers to the way data are organized and stored.

- **Structured datasets**

<div class="text-box">
Data are organized in predefined formats such as rows and columns, following a consistent schema.
This structure facilitates easy access, querying, and statistical analysis.<br><br>
Examples: Student grade tables, customer databases, sales records.
</div>

- **Unstructured datasets**

<div class="text-box">
Data lack a fixed organizational model or tabular format.
They are often textual, visual, or multimedia, and require preprocessing before analysis.<br><br>
Examples: Text documents, emails, images, videos, audio recordings.
</div>

- **Semi-structured datasets**

<div class="text-box">
Data contain some elements of organization but do not adhere to a rigid table structure.
They often use tags or key-value pairs to provide partial structure.<br><br>
Examples: JSON files, XML data, web server logs.
</div>

### **2. Based on Measurement Type**

This classification considers the nature of the variables in the dataset — whether they represent numerical quantities or descriptive categories.

- **Quantitative datasets**

<div class="text-box">
  Variables are numerical and express measurable quantities.<br><br>
  They can be:
  <ul>
    <li><strong>Continuous:</strong> values can take any point within a range (e.g., height, temperature).</li><br>
    <li><strong>Discrete:</strong> values are countable (e.g., number of siblings, daily sales).
  </li>
  </ul>
  Examples: Students exam scores, daily rainfall, monthly income.
</div>

- **Qualitative datasets**

<div class="text-box">
  Variables are categorical and describe attributes or characteristics rather than numbers.<br><br>
  They can be:
  <ul>
    <li><strong>Nominal:</strong> unordered categories (e.g., gender, nationality).</li><br>
    <li><strong>Ordinal:</strong> ordered categories (e.g., satisfaction level, education rank).<br><br>
  </li>
  </ul>
  Examples: Favorite colors, job titles, survey responses.
</div>

## **The Concept of a Distribution**

A **distribution** describes how the values of a **variable** (or several variables) are spread across a **dataset**.
It provides insight into how frequently different outcomes occur and is the foundation for understanding patterns, tendencies, and variability within data.
In statistics, and especially in cybersecurity analytics, studying **data distributions** is crucial for detecting anomalies, understanding normal system behavior, and identifying unusual user activities.

## **Types of Distributions**

### **1. Distributions Based on the Number of Variables**

Statistical distributions can also be classified according to the number of variables being analyzed.
This criterion determines the dimensionality of the analysis, whether it involves one, two, or several variables simultaneously.

- **Univariate distribution:** describes the characteristics of a single variable. It focuses on understanding how the values of that variable are distributed within a dataset. Typical descriptive measures include the mean, median, mode, variance, and standard deviation. Univariate analysis provides a simple yet essential view of data behavior.

- **Bivariate distribution:** examines the relationship between two variables. It allows researchers to identify possible associations, correlations, or dependencies between them. For example, analyzing Age and Height together can reveal whether taller individuals tend to be older or younger on average. Visual representations such as scatter plots or two-way frequency tables are often used in this context.

- **Multivariate distribution:** extends this concept to three or more variables. It investigates more complex interactions and dependencies, often using multidimensional approaches such as correlation matrices, principal component analysis, or multivariate regression models. This type of distribution is common in advanced data analysis and predictive modeling, where multiple factors influence an outcome simultaneously.

Understanding the number of variables considered in a distribution is crucial for selecting the appropriate statistical tools and visualization techniques.

### **2. Distributions Based on the Nature of the Distribution**

Distributions can also be categorized according to the nature of the information they provide.
In this sense, we distinguish among frequency distributions, probability distributions, and cumulative distributions.

- **Frequency distribution:** shows how often each value or range of values appears in a dataset. It is descriptive in nature and provides a direct view of how data are spread across different categories or intervals. Frequency distributions are typically represented through tables, bar charts, or histograms, and they form the foundation of descriptive statistics.

- **Probability distribution:** expresses the likelihood that each possible value of a random variable will occur. Unlike frequency distributions, which are based on observed data, probability distributions often represent theoretical models such as the normal, binomial, or Poisson distributions. In this case, the sum of all probabilities equals one. Probability distributions are essential for inferential statistics, hypothesis testing, and risk modeling.

- **Cumulative distribution:** shows the running total of frequencies or probabilities up to a given value. It answers questions such as “What proportion of observations fall below or equal to a certain threshold?” and is often displayed as an increasing curve that approaches one (or 100%). Cumulative distributions are particularly useful for identifying percentiles and understanding the overall shape of data accumulation.

In summary, frequency distributions describe what has been observed, probability distributions model what is likely to occur, and cumulative distributions show how values build up over a range. Together, these perspectives provide a comprehensive understanding of how data behave and how uncertainty can be modeled statistically.

## **Example Dataset**

The following dataset, named `Table`, will be used to demonstrate both univariate and bivariate analyses.
The table includes three variables (Age, Height(cm), and Gender) representing a simplified sample of user data.

![Table](/assets/images/Table.png)

### **Example of Univariate Analysis**

```sql
SELECT
    Height(cm),
    COUNT (*) AS frequency
FROM
    Table
GROUP BY
    Height(cm)
ORDER BY
    Height(cm);
```
![Height frequency](/assets/images/Height%20frequency.png)

This query analyzes the distribution of the variable Height (cm).
By grouping all records according to height values and counting their occurrences, it calculates the frequency of each distinct height in the dataset.
The resulting frequency table shows how many individuals share the same height.
For example, a height of 165 cm appears twice, while most other heights occur only once.
This allows us to visualize how height values are distributed across the sample and to detect any repeated or uncommon measurements.

```sql
SELECT
    Age,
    COUNT (*) AS frequency
FROM
    Table
GROUP BY
    Age
ORDER BY
    Age;
```
![Age frequency](/assets/images/Age%20frequency.png)

This query performs a similar analysis on the variable Age.
It groups the data by age and counts how many individuals belong to each age group, producing a frequency distribution of ages.
From the results, we can observe that certain ages (e.g., 19 and 20) occur more frequently, suggesting that the dataset contains a higher number of individuals within that age range.

### **Example of Bivariate Analysis (Variables: Age and Gender)**

```sql
SELECT
    Age,
    Gender
    COUNT (*) AS frequency
FROM
    Table
GROUP BY
    Age,
    Gender
ORDER BY
    Age,
    Gender;
```
![Age-Gender frequency](/assets/images/Age-Gender.png)

This query performs a bivariate analysis by examining the relationship between two variables: Age and Gender.
By grouping the data according to both variables and counting the number of records for each pair, the query calculates the joint frequency distribution.
The resulting table shows, for example, how many males and females correspond to each age value in the dataset.
From the results, it can be observed that certain age groups (such as 19 and 20) have a higher frequency, and that the distribution between genders varies slightly within those ages.
This analysis provides insight into how the two variables interact — revealing patterns such as age groups where one gender may be more represented than the other.
Bivariate analysis is useful for identifying associations or differences between two variables and forms the basis for more advanced statistical studies, such as correlation or regression analysis.

---

# **Caesar Cipher Demonstration and Implementation**

# **Introduction**
Cryptography and statistics, though seemingly distinct, share a deep mathematical connection. The **Caesar cipher**, one of the oldest encryption schemes, replaces each letter with another a fixed number of positions down the alphabet. While trivial from a modern cryptographic perspective, it offers an excellent framework to demonstrate how statistical distribution analysis can be applied to cryptanalysis.
This work presents a statistical method for decrypting the classical Caesar cipher by exploiting the **frequency distribution** of English letters. Using a combination of brute-force search and chi-squared goodness-of-fit testing, the encryption key (shift) is automatically inferred. The project illustrates how simple statistical inference and frequency analysis can transform a deterministic decryption task into a probabilistic inference problem.
An HTML/JavaScript implementation provides an interactive demonstration designed for educational use in statistics and cryptography courses.


## **Goals of the Implementation**
The primary goals of this project are:

- To **demonstrate** how statistical inference can be used to decrypt a substitution cipher.

- To **compare** observed letter frequencies in a ciphertext with expected English frequencies.

- To **identify** the shift parameter that minimizes the chi-squared distance between distributions.

- To **implement** and visualize the method using JavaScript in an interactive web interface.
<br><br>

## **Example of the Caesar Cipher**

A Caesar cipher with a shift of **+3** transforms the alphabet as follows:

<div class="example-box">
<strong>Plaintext Alphabet:</strong>&nbsp;	A	B	C	D	E	F	G	H	I	J	K	L	M	N	O	P	Q	R	S	T	U	V	W	X	Y	Z<br>
<strong>Ciphertext Alphabet:</strong>	D	E	F	G	H	I	J	K	L	M	N	O	P	Q	R	S	T	U	V	W	X	Y	Z	A	B	C
</div>

Example:<br>

<strong>Plaintext:</strong> HELLO WORLD<br>
<strong>Ciphertext (shift +3):</strong> KHOOR ZRUOG<br>

The encryption rule can be expressed mathematically as:<br>

<div class="formula-box">
E(x) = (x+k) mod 26
</div>

and the decryption as:<br>

<div class="formula-box">
D(x) = (x−k) mod 26
</div>

where k is the shift amount and x represents the alphabetical index (A=0, …, Z=25). <br>

---

## **Caesar Cipher Demo**

<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Caesar Cipher — Live Encrypt + Analyze</title>
</head>
<body>
  <section id="main_content" class="container">
    <div class="cipher-container">
      <div class="cipher-box">
        <!-- Plain text -->
        <div class="textarea-container">
          <h3>Plaintext</h3>
          <textarea id="plain" placeholder="Type or paste your original text..." spellcheck="false"></textarea>
        </div>
        <!-- Cipher text -->
        <div class="textarea-container">
          <div class="row-inline space-between">
            <h3 class="m0">Ciphertext</h3>
          </div>
          <textarea id="cipher" placeholder="Auto-filled using Caesar Cipher +3" spellcheck="false" readonly></textarea>
          <label class="row-inline" title="Enable manual editing for the cipher box">
              <input type="checkbox" id="toggleManualCipher" />
              <span>Enable manual ciphertext input</span>
            </label>
        </div>
        <!-- Buttons -->
        <div class="buttons">
          <button id="analyze">Analyze</button>
          <button id="reset" class="danger">Reset</button>
        </div>
        <!-- Charts -->
        <section class="chart-grid">
          <div class="chart-card">
            <h3 class="chart-title">Letter distribution — Plaintext</h3>
            <canvas id="chartPlain"></canvas>
          </div>
          <div class="chart-card">
            <h3 class="chart-title">Letter distribution — Ciphertext</h3>
            <canvas id="chartCipher"></canvas>
          </div>
        </section>
        <div id="decodeOutput" class="result-panel">
        </div>
      </div>
    </div>
  </section>

  <script>
  (function(){
    // --- English letter frequencies A..Z (for chi-squared fit) ---
    const freqEn = {
      A:8.17,B:1.49,C:2.78,D:4.25,E:12.70,F:2.23,G:2.02,H:6.09,I:6.97,J:0.15,K:0.77,L:4.03,M:2.41,
      N:6.75,O:7.51,P:1.93,Q:0.10,R:5.99,S:6.33,T:9.06,U:2.76,V:0.98,W:2.36,X:0.15,Y:1.97,Z:0.07
    };

    // --- HiDPI / responsive canvas setup ---
    function setupHiDPI(canvas) {
      const dpr = window.devicePixelRatio || 1;
      const rect = canvas.getBoundingClientRect();
      const cssW = Math.max(1, Math.round(rect.width));
      const cssH = Math.max(1, Math.round(rect.height));
      const needResize = (canvas.width !== Math.round(cssW * dpr)) || (canvas.height !== Math.round(cssH * dpr));
      if (needResize) {
        canvas.width  = Math.round(cssW * dpr);
        canvas.height = Math.round(cssH * dpr);
      }
      const ctx = canvas.getContext('2d');
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
      return { ctx, dpr, cssW, cssH };
    }

    // --- Helpers ---
    const mod = (n, m) => ((n % m) + m) % m;
    function onlyLetters(s){ return (s||'').toUpperCase().replace(/[^A-Z]/g,''); }
    function letterCounts(s){
      const counts = Array(26).fill(0);
      const clean = onlyLetters(s);
      for (const ch of clean) counts[ch.charCodeAt(0) - 65]++;
      return { counts, total: clean.length };
    }
    function toPercent(counts, total){ return counts.map(c => total ? (c * 100 / total) : 0); }
    function chiSquared(obsPerc, expectedPerc){
      let chi = 0;
      for (let i = 0; i < 26; i++){
        const O = obsPerc[i], E = expectedPerc[i];
        if (E > 0) chi += ((O - E) * (O - E)) / E;
      }
      return chi;
    }
    function pad2(n){ return String(n).padStart(2,' '); }
    function countsBlock(label, counts, total){
      const parts = [];
      for (let i=0;i<26;i++){
        const ch = String.fromCharCode(65+i);
        parts.push(`${ch}:${pad2(counts[i]||0)}`);
      }
      const line1 = parts.slice(0,13).join('  ');
      const line2 = parts.slice(13).join('  ');
      return `${label} — total letters: ${total}\n${line1}\n${line2}`;
    }

    function updateResultsPanel(){
      const plainText  = (elPlain.value  || '');
      const cipherText = (elCipher.value || '');

      const { counts:c1, total:t1 } = letterCounts(plainText);
      const { counts:c2, total:t2 } = letterCounts(cipherText);

      let out = countsBlock('Plain',  c1, t1) + '\n\n' +
                countsBlock('Cipher', c2, t2);

      decodeOut.textContent = out;
    }

    function renderAutoDecodePanel(){
      const cipherText = (elCipher.value || '').trim();
      if (!cipherText.length){
        decodeOut.textContent = '';
        decodeOut.classList.remove('visible');
        return;
      }
      const auto = autoDecode(cipherText);
      const autoLine = `Auto-Decode (estimated shift: ${auto.shift})\n${auto.text}\n\n`;

      const cleanedCounts = decodeOut.textContent.trimStart();

      decodeOut.textContent = autoLine + cleanedCounts;
      decodeOut.classList.add('visible');
    }

    // --- Caesar cipher ---
    function caesarEncode(str, shift) {
      let out = '';
      for (const c of str){
        if (/[A-Za-z]/.test(c)){
          const base = (c === c.toUpperCase()) ? 65 : 97;
          const code = c.charCodeAt(0) - base;
          out += String.fromCharCode(mod(code + shift, 26) + base);
        } else out += c;
      }
      return out;
    }
    function caesarDecode(str, shift){ return caesarEncode(str, -shift); }

    // --- Brute force (1..25) ---
    function bruteForceDecode(text){
      const results = [];
      for (let shift = 1; shift < 26; shift++){
        results.push({ shift, text: caesarDecode(text, shift) });
      }
      return results;
    }

    // --- Auto-decode via chi-squared ---
    function autoDecode(text){
      const { counts, total } = letterCounts(text);
      if (total === 0) return { text: '', shift: 0, chi: Infinity };
      const obsPerc = toPercent(counts, total);
      const expectedArr = Object.keys(freqEn).map(k => freqEn[k]);
      let bestShift = 0, bestChi = Infinity;
      for (let shift = 0; shift < 26; shift++){
        const rotated = obsPerc.map((_, i) => obsPerc[mod(i + shift, 26)]);
        const chi = chiSquared(rotated, expectedArr);
        if (chi < bestChi){ bestChi = chi; bestShift = shift; }
      }
      return { text: caesarDecode(text, bestShift), shift: bestShift, chi: bestChi };
    }

    // --- Minimal popup (with scroll lock and edge-guard) ---
    function showPopup({title = 'Output', content = '', nextLabel = null, onNext = null} = {}){
      const backdrop = document.createElement('div'); backdrop.className = 'c-popup-backdrop';
      const popup = document.createElement('div'); popup.className = 'c-popup';
      const header = document.createElement('div'); header.className = 'c-popup__header'; header.textContent = title;
      const body = document.createElement('div'); body.className = 'c-popup__body';
      const pre = document.createElement('pre'); pre.className = 'c-popup__content'; pre.textContent = content;
      const actions = document.createElement('div'); actions.className = 'c-popup__actions';

      const closeBtn = document.createElement('button'); closeBtn.className = 'c-btn';
      closeBtn.textContent = onNext ? 'Close' : 'OK'; closeBtn.addEventListener('click', cleanup);
      actions.appendChild(closeBtn);

      if (onNext) {
        const nextBtn = document.createElement('button'); nextBtn.className = 'c-btn';
        nextBtn.textContent = nextLabel || 'Next';
        nextBtn.addEventListener('click', () => { cleanup(); onNext(); });
        actions.appendChild(nextBtn);
      }

      body.appendChild(pre); popup.appendChild(header); popup.appendChild(body); popup.appendChild(actions);

      // Prevent background scroll while popup is open
      const previousBodyOverflow = document.body.style.overflow;
      document.body.classList.add('no-scroll');

      // Prevent scroll "leaking" to the page when the content reaches top/bottom
      const wheelGuard = (e) => {
        const el = pre;
        const delta = e.deltaY;
        const atBottom = el.scrollTop + el.clientHeight >= el.scrollHeight - 1;
        const atTop = el.scrollTop <= 0;
        if ((delta > 0 && atBottom) || (delta < 0 && atTop)) {
          e.preventDefault();
        }
        e.stopPropagation();
      };
      pre.addEventListener('wheel', wheelGuard, { passive: false });
      pre.addEventListener('touchmove', (e) => {
        // Basic touch guard: stop propagation to body
        e.stopPropagation();
      }, { passive: false });

      // Block page-scroll keys while popup is focused
      const keyGuard = (e) => {
        const keys = [' ', 'PageDown', 'PageUp', 'ArrowDown', 'ArrowUp', 'Home', 'End'];
        if (keys.includes(e.key)) {
          e.preventDefault();
        }
      };
      popup.addEventListener('keydown', keyGuard);

      function cleanup(){
        try { document.body.removeChild(backdrop); } catch(e){}
        try { document.body.removeChild(popup); } catch(e){}
        document.body.classList.remove('no-scroll');
        document.body.style.overflow = previousBodyOverflow || '';
        pre.removeEventListener('wheel', wheelGuard);
        popup.removeEventListener('keydown', keyGuard);
      }

      backdrop.addEventListener('click', cleanup);
      document.body.appendChild(backdrop); 
      document.body.appendChild(popup);

      // Focus popup to capture key events
      popup.setAttribute('tabindex', '-1');
      popup.focus();
    }

    // --- Chart drawing ---
    function drawAxesOnly(canvas){
      const { ctx, dpr } = setupHiDPI(canvas);
      const W = canvas.width  / dpr, H = canvas.height / dpr;
      ctx.clearRect(0,0,W,H);

      const pad = { top: 12, right: 10, bottom: 32, left: 40 };
      const plotW = W - pad.left - pad.right;
      const plotH = H - pad.top - pad.bottom;
      const px = 0.5;

      // Axes
      ctx.strokeStyle = '#63c0f5';
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(pad.left + px, pad.top + px);
      ctx.lineTo(pad.left + px, pad.top + plotH + px);
      ctx.lineTo(pad.left + plotW + px, pad.top + plotH + px);
      ctx.stroke();

      // X labels
      ctx.fillStyle = '#eaeaea';
      ctx.font = '10px Monaco, Consolas, monospace';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'top';
      const barGap = 2;
      const barW = Math.max(1, Math.floor(plotW / 26) - barGap);
      for (let i=0;i<26;i++){
        const x = pad.left + i * (barW + barGap) + barW/2 + 1;
        ctx.fillText(String.fromCharCode(65+i), x, pad.top + plotH + 4);
      }

      // Y ticks 0..10 (empty state)
      ctx.textAlign = 'right';
      ctx.textBaseline = 'middle';
      ctx.fillStyle = '#eaeaea';
      for (let y=0; y<=10; y++){
        const yy = pad.top + plotH - ((y/10) * (plotH - 10));
        ctx.fillText(String(y), pad.left - 6, yy);
      }
    }

    function drawBarChartCounts(canvasId, counts, title = ''){
      const canvas = document.getElementById(canvasId);
      const { ctx, dpr } = setupHiDPI(canvas);
      const W = canvas.width / dpr, H = canvas.height / dpr;

      ctx.clearRect(0,0,W,H);

      const pad = { top: 16, right: 10, bottom: 32, left: 40 };
      const plotW = W - pad.left - pad.right;
      const plotH = H - pad.top - pad.bottom;

      // Title
      if (title){
        ctx.fillStyle = '#b5e853';
        ctx.font = '13px Monaco, Consolas, monospace';
        ctx.textAlign = 'left';
        ctx.textBaseline = 'alphabetic';
        ctx.fillText(title, pad.left + 2, pad.top - 4);
      }

      const maxCount = Math.max(10, ...(counts || [0]));
      const yTop = maxCount;
      const px = 0.5;

      // Axes
      ctx.strokeStyle = '#63c0f5';
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(pad.left + px, pad.top + px);
      ctx.lineTo(pad.left + px, pad.top + plotH + px);
      ctx.lineTo(pad.left + plotW + px, pad.top + plotH + px);
      ctx.stroke();

      // Bars
      const barGap = 2;
      const barW = Math.max(1, Math.floor(plotW / 26) - barGap);
      ctx.fillStyle = '#b5e853';
      for (let i=0;i<26;i++){
        const v = counts[i] || 0;
        const h = (v / yTop) * (plotH - 10);
        const x = pad.left + i * (barW + barGap) + 1;
        const y = pad.top + plotH - h;
        ctx.fillRect(x, y, barW, h);
      }

      // X labels
      ctx.fillStyle = '#eaeaea';
      ctx.font = '10px Monaco, Consolas, monospace';
      ctx.textAlign = 'center';
      ctx.textBaseline = 'top';
      for (let i=0;i<26;i++){
        const x = pad.left + i * (barW + barGap) + barW/2 + 1;
        ctx.fillText(String.fromCharCode(65+i), x, pad.top + plotH + 4);
      }

      // Y ticks and labels: show ALL integers 0..maxCount
      ctx.textAlign = 'right';
      ctx.textBaseline = 'middle';
      ctx.fillStyle = '#eaeaea';
      ctx.strokeStyle = 'rgba(99,192,245,0.25)'; // light grid lines
      ctx.lineWidth = 1;

      for (let y=0; y<=yTop; y++){
        const yy = pad.top + plotH - ((y / yTop) * (plotH - 10));
        // Optional light horizontal grid
        ctx.beginPath();
        ctx.moveTo(pad.left + px, yy + px);
        ctx.lineTo(pad.left + plotW + px, yy + px);
        ctx.stroke();

        ctx.fillText(String(y), pad.left - 6, yy);
      }
    }

    // --- UI wiring ---
    const elPlain  = document.getElementById('plain');
    const elCipher = document.getElementById('cipher');
    const btnAnalyze = document.getElementById('analyze');
    const btnReset   = document.getElementById('reset');
    const cPlain  = document.getElementById('chartPlain');
    const cCipher = document.getElementById('chartCipher');
    const chkManual = document.getElementById('toggleManualCipher');
    const decodeOut = document.getElementById('decodeOutput');

    function updateCipherLive(){
      if (chkManual.checked) return;
      const raw = elPlain.value || '';
      elCipher.value = caesarEncode(raw, 3);
    }

    function redrawCharts() {
      const plainText  = (elPlain.value || '').trim();
      const cipherText = (elCipher.value || '').trim();

      if (!plainText.length) drawAxesOnly(cPlain);
      else {
        const { counts: c1 } = letterCounts(plainText);
        drawBarChartCounts('chartPlain',  c1, '');
      }

      if (!cipherText.length) drawAxesOnly(cCipher);
      else {
        const { counts: c2 } = letterCounts(cipherText);
        drawBarChartCounts('chartCipher', c2, '');
      }
    }

    // Init
    updateCipherLive();
    redrawCharts();

    // Events
    elPlain.addEventListener('input', () => { 
      if (chkManual.checked) {
        chkManual.checked = false;
        elCipher.readOnly = true;
        elCipher.value = '';
      }
      updateCipherLive();
      redrawCharts();
    });

    elCipher.addEventListener('input', () => {
      if (chkManual.checked) {
        redrawCharts();
      }
    });

    chkManual.addEventListener('change', () => {
      elCipher.readOnly = !chkManual.checked;

      if (chkManual.checked){
        elPlain.value = '';
      } else {
        updateCipherLive();
      }

      redrawCharts();
    });

    btnAnalyze.addEventListener('click', () => {
      const plainText  = (elPlain.value || '').trim();
      const cipherText = (elCipher.value || '').trim();
      if (!plainText.length && !cipherText.length){
        showPopup({ title: 'Notice', content: 'Please enter some text in either Plain or Cipher.' });
        return;
      }
      redrawCharts();

      updateResultsPanel();

      const all = bruteForceDecode(cipherText);
      const bruteOutput = all.map(r => `Shift ${r.shift}:\n${r.text}`).join('\n\n');

      const auto = autoDecode(cipherText);
      const autoMsg = `Auto-Decode Result\n\nEstimated shift: ${auto.shift}\n\n${auto.text}`;

      showPopup({
        title: 'Brute-force: all shifts (1..25)',
        content: bruteOutput,
        nextLabel: 'Show auto-decode',
        onNext: () => {
          showPopup({
          title: 'Auto-decode via frequency analysis',
          content: autoMsg
        });
        renderAutoDecodePanel();
      }
      });
    });

    // Reset button: clears fields, unchecks manual mode, restores readOnly
    btnReset.addEventListener('click', () => {
      elPlain.value = '';
      elCipher.value = '';
      chkManual.checked = false;
      elCipher.readOnly = true;
      updateCipherLive();
      redrawCharts();
      decodeOut.textContent = '';
      decodeOut.classList.remove('visible');
      elPlain.focus();
    });
    window.addEventListener('resize', redrawCharts);
    })();
  </script>
</body>
</html>



---

## **Methods**

### **1. English Letter Frequency Distribution**

Empirical linguistic studies provide the average percentage frequency of each English letter:

<div class="table_2-box">
<table>
  <thead>
    <tr>
      <th>Letter</th>
      <th>A</th><th>B</th><th>C</th><th>D</th><th>E</th><th>F</th><th>G</th><th>H</th><th>I</th><th>J</th><th>K</th><th>L</th><th>M</th><th>N</th><th>O</th><th>P</th><th>Q</th><th>R</th><th>S</th><th>T</th><th>U</th><th>V</th><th>W</th><th>X</th><th>Y</th><th>Z</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>%</td>
      <td>8.17</td><td>1.49</td><td>2.78</td><td>4.25</td><td>12.70</td><td>2.23</td><td>2.02</td><td>6.09</td><td>6.97</td><td>0.15</td><td>0.77</td><td>4.03</td><td>2.41</td><td>6.75</td><td>7.51</td><td>1.93</td><td>0.10</td><td>5.99</td><td>6.33</td><td>9.06</td><td>2.76</td><td>0.98</td><td>2.36</td><td>0.15</td><td>1.97</td><td>0.07</td>
    </tr>
  </tbody>
</table>
</div>

These serve as the expected probabilities E<sub>i</sub> in the chi-squared comparison.
<br><br>

### **2. Chi-Squared Test**

To estimate the shift k, the algorithm compares observed and expected frequencies using the chi-squared statistic:

<div class="formula-box">
χ<sup>2</sup> = ∑<sub>i=1</sub><sup>26</sup> 
  <span>(O<sub>i</sub> − E<sub>i</sub>)<sup>2</sup></span> / E<sub>i</sub>
</div>

where:<br>
- O<sub>i</sub>(k) = observed frequency of the i-th letter after applying shift k<br>

- E<sub>i</sub> = expected frequency from English distribution

The smallest **χ²** indicates the **best statistical match**.
Degrees of freedom ≈ 25 (26 letters − 1).
This test is used here as a ranking score, not a formal hypothesis test.

## **Algorithmic Steps**

The algorithm used for automatic decryption follows these steps:

### 1. **Input acquisition**  

<div class="text-box">Read the <strong>ciphertext</strong> provided by the user, or automatically generate it from the plaintext using a fixed shift of <strong>+3.</strong></div>

### 2. **Preprocessing and sanitization**

<div class="text-box">Keep only uppercase letters <strong>A–Z</strong> for frequency analysis, removing all other characters.</div>

### 3. **Frequency computation**

<div class="text-box">Count the occurrences of each letter and compute the <strong>percentage frequency</strong> of every letter in the ciphertext.</div>

### 4. **Chi-squared evaluation**

<div class="text-box">For each possible shift value <strong>( k = 0, 1, ..., 25 )</strong>:  

  - Rotate the observed frequency vector by <strong>+k</strong> positions.

  - Compute the chi-squared statistic by comparing the rotated frequencies with the expected English letter frequencies.</div>

### 5. **Key estimation**  

<div class="text-box">Identify the shift that produces the <strong>smallest chi-squared value</strong>.  

  - This represents the <strong>estimated shift</strong> that best matches English letter statistics.</div>

### 6. **Decryption**

<div class="text-box">Apply <strong style="color: #FFA500">caesarDecode(str, shift)</strong> to recover the plaintext.</div>

### 7. **Visualization and output**  
   
<div class="text-box">Display results through:  
  
  - A popup listing <strong>all brute-force candidates (1–25)</strong>.

  - A second popup showing the <strong>auto-decoded</strong> result and estimated shift.  

  - An on-page <strong>results panel</strong> and <strong>bar charts</strong> showing the letter distributions.</div>

## **Overview of the Implementation**
The system is implemented in **HTML + JavaScript**, fully client-side, without external libraries.
The JavaScript program implements both:

- Brute-force decryption over all Caesar shifts (1..25)

- Statistical key detection via a chi-squared fit to English letter frequencies.

The UI features live encryption (default shift +3), optional manual ciphertext input, a two-step analysis flow (brute-force → auto-decode), a results panel, and two bar charts depicting absolute letter counts for Plain and Cipher texts.
<br><br>

![Code](/assets/images/code.png)
<br>

### **1. Interface Overview**

- **Plain text:** free-form input area.

- **Cipher text:** auto-filled with a +3 Caesar shift while you type in Plain text.

- **Enable manual ciphertext input:** when checked, you can edit the ciphertext manually (auto-fill is disabled).

- **Analyze:** runs brute-force over all shifts (1..25) and automatic decoding via the chi-squared method; shows two popups in sequence and writes the result into the on-page results panel.

- **Reset:** clears fields, disables manual mode, and restores defaults.

- **Charts:** two canvases render letter distributions (absolute counts) for Plain and Cipher; HiDPI-safe rendering, no external libraries.
<br><br>

### **2. Caesar Encoder/Decoder**

```js
function caesarEncode(str, shift) {
  let out = '';
  for (const c of str){
    if (/[A-Za-z]/.test(c)){
      const base = (c === c.toUpperCase()) ? 65 : 97;
      const code = c.charCodeAt(0) - base;
      out += String.fromCharCode(mod(code + shift, 26) + base);
    } else out += c;
  }
  return out;
}

function caesarDecode(str, shift){
  return caesarEncode(str, -shift);
}
```
The **Caesar encoder** is responsible for applying a uniform cyclic shift to each alphabetic character in the input string. It supports both uppercase and lowercase letters while leaving non-alphabetic symbols (such as punctuation and spaces) unchanged. The function relies on modular arithmetic to wrap character codes within the 26-letter Latin alphabet.

**Explanation:**


- **Character filtering**

<div class= "text-box">The regular expression <strong>/[A-Za-z]/</strong> ensures that only alphabetic characters are shifted; all other characters are appended unchanged, preserving spacing and punctuation.</div>

- **Case preservation**

<div class="text-box">The variable base stores the ASCII code of either 'A' (65) or 'a' (97), allowing the algorithm to maintain case by computing offsets relative to the correct alphabet segment.</div>

- **Modular wrap-around**

<div class="text-box"> The expression <strong>((code + shift + 26) % 26)</strong> ensures cyclic behavior. For instance, shifting 'Z' by +1 produces 'A'. Adding 26 before the modulo prevents negative results during decoding.</div>

- **Symmetric decoding**

<div class="text-box"> The decoder simply calls the encoder with a negated shift, guaranteeing functional symmetry:
  <strong style="colore: #FFA500">decode(encode(x, k), k) = x.</strong>
  This design centralizes all transformation logic in one function, minimizing redundancy and potential inconsistencies.</div>

- **Integration in the interface**

<div class="text-box"> Within the application, this encoder is invoked in real time whenever the user types in the Plain text field, automatically producing the Cipher text using a default shift of +3. When “manual ciphertext input” is enabled, this
automatic behavior is temporarily disabled, allowing the user to input arbitrary encrypted data.</div>

### **3. Brute-Force Decryption**

**Brute-force decryption** is implemented to demonstrate the inherent weakness of the Caesar cipher against exhaustive key search. Since the cipher’s key space contains only 25 non-trivial shifts (from 1 to 25), every possible transformation can be generated and displayed almost instantaneously.
<br>

```js
function bruteForceDecode(text){
  const results = [];
  for (let shift = 1; shift < 26; shift++){
    results.push({ shift, text: caesarDecode(text, shift) });
  }
  return results;
}
```

### **4. Frequency Count and Normalization**

This phase focuses on extracting the **statistical characteristics** of the text by analyzing how frequently each letter appears.
All non-alphabetic characters are removed, and the remaining letters are converted into a uniform format to ensure consistency.
The total occurrences of each letter are then transformed into percentages, allowing the **frequency distribution** of the text to be compared objectively with other samples, regardless of length or content.
This normalization process is fundamental for the later statistical comparison used in decryption.

```js
function onlyLetters(s){ return (s||'').toUpperCase().replace(/[^A-Z]/g,''); }

function letterCounts(s){
  const counts = Array(26).fill(0);
  const clean = onlyLetters(s);
  for (const ch of clean) counts[ch.charCodeAt(0) - 65]++;
  return { counts, total: clean.length };
}

function toPercent(counts, total){
  return counts.map(c => total ? (c * 100 / total) : 0);
}
```

### **5. Chi-Squared Fit**

In this phase, the observed **frequency distribution** of letters in the **encrypted text** is statistically compared with the typical distribution of letters in English.
The **Chi-squared** test quantifies the difference between these two distributions, producing a numerical value that expresses how closely the text resembles natural English.
A lower Chi-squared value indicates a stronger match, suggesting that the applied shift aligns the ciphertext more accurately with standard linguistic patterns.
This statistical fit is the foundation of the automatic key detection process.

```js
const freqEn = {
  A:8.17,B:1.49,C:2.78,D:4.25,E:12.70,F:2.23,G:2.02,H:6.09,I:6.97,J:0.15,K:0.77,L:4.03,M:2.41,
  N:6.75,O:7.51,P:1.93,Q:0.10,R:5.99,S:6.33,T:9.06,U:2.76,V:0.98,W:2.36,X:0.15,Y:1.97,Z:0.07
};

function chiSquared(obsPerc, expectedPerc){
  let chi = 0;
  for (let i = 0; i < 26; i++){
    const O = obsPerc[i], E = expectedPerc[i];
    if (E > 0) chi += ((O - E) * (O - E)) / E;
  }
  return chi;
}
```
### **6. Automatic Decoding via Frequency Rotation**

The automatic decoding phase uses the **frequency data** and **Chi-squared** comparison to identify the most probable Caesar shift.
It systematically rotates the frequency distribution across all **26** possible positions and calculates the Chi-squared score for each rotation.
The shift that yields the smallest value is considered the best statistical match and is therefore used to reconstruct the likely plaintext.
This approach allows the cipher to be **decrypted** without knowing the key in advance, relying purely on probabilistic and linguistic evidence.

```js
function autoDecode(text){
  const { counts, total } = letterCounts(text);
  if (total === 0) return { text: '', shift: 0, chi: Infinity };

  const obsPerc = toPercent(counts, total);
  const expectedArr = Object.keys(freqEn).map(k => freqEn[k]);

  let bestShift = 0, bestChi = Infinity;

  for (let shift = 0; shift < 26; shift++){
    const rotated = obsPerc.map((_, i) => obsPerc[mod(i + shift, 26)]);
    const chi = chiSquared(rotated, expectedArr);
    if (chi < bestChi){ bestChi = chi; bestShift = shift; }
  }
  return { text: caesarDecode(text, bestShift), shift: bestShift, chi: bestChi };
}
```

### **7. User Interface and Interaction Flow**

- **Typing:** as you type in Plain text, Cipher text updates in real time with shift +3 (unless manual mode is enabled).

- **Analyze:**
  1. A **Brute-force** popup lists all candidate plaintexts (Shift 1 → 25).

  2. An **Auto-decode** popup shows the **estimated shift** and **decoded text** via the chi-squared fit.

  3. The auto-decode result is also written to the results panel below the charts.

- **Reset:** clears inputs and charts, disables manual mode, and focuses *Plain text*.
 
## **Conclusion**
**The Caesar Cipher Demo** illustrates the fundamental principles of **symmetric encryption** and **classical cryptanalysis**.
Although insecure for real applications, it provides a valuable didactic tool for understanding how patterns in natural language can reveal hidden information.
The integration of frequency analysis, implemented entirely in client-side JavaScript, demonstrates how **computational methods** can approximate **linguistic inference**, marking a clear link between cryptography, data analysis, and computer science education.

---

[← Back to Home](/)
