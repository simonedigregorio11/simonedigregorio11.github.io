---
title: "Homework 5"
layout: default
permalink: /homework5/
---

<link rel="stylesheet" href="/assets/css/pages/homework5.css">

[← Back to Home](/)

---
# **Measures of Location and Dispersion**

# **Introduction**

In statistics, a **measure of location**, often called an **average** or **measure of central tendency**, is a single
numerical value that represents the “typical” or “central” point of a dataset. Such measures are crucial for summarizing
data, describing populations, and making comparisons across different samples. They condense complex sets of
observations into a manageable and interpretable form, allowing researchers to communicate findings efficiently and to
detect anomalies, trends, or asymmetries.

However, not all data behave in the same way, and not all averages convey the same information. The choice of which
**measure of location** to use depends on the **nature of the variable**, the **presence of outliers**, and the
**conceptual meaning** of “central” within the given context. This essay discusses the main **measures of location**
that have been proposed, explains **how they are calculated**, and clarifies **when each of them is meaningful or
useful**.

## **The Arithmetic Mean**

The **arithmetic mean** is the most familiar **measure of location**. It is calculated as the **sum of all data values
divided by the number of observations**:

<div class="formula-box">
    x&#772; = ∑<sub>i=1</sub><sup>n</sup> x<sub>i</sub> / n
</div>
​

It represents the point at which the dataset would balance if its values were weights placed on a scale. Because of its
mathematical properties, it plays a central role in **inferential statistics**, **estimation**, and **hypothesis
testing**.

<div class="example-box">
<strong>Example</strong><br>
Consider five exam scores: 60, 70, 80, 90, and 100.<br>
The arithmetic mean is: 

<div class="formula-box_2">
(60 + 70 + 80 + 90 + 100) / 5 = 80
</div>
which indicates the “average” performance of the students.
</div>

- **When useful**<br>
The arithmetic mean works best when the distribution is **symmetric** and **free of extreme outliers**. In such cases,
it captures the true **central value** and provides a meaningful basis for comparison. It is also appropriate when every
observation contributes **equally** to the total — such as in physical measurements or laboratory data.

- **When misleading**<br>
If the data are **skewed**, the mean may be distorted by **extreme values**.<br>
For example, in a dataset of annual incomes {20k, 22k, 25k, 30k, 200k}, the mean is 59.4k, which does not represent the
majority.<br>
In such situations, other measures like the **median** provide a more realistic picture.

## **The Median**

The **median** is the value that **divides a dataset into two equal halves** when ordered from smallest to largest. If
the number of observations is odd, the median is the **middle value**; if even, it is the **average of the two central
values**.

<div class="example-box">
<strong>Example</strong><br>
For the exam scores {60, 70, 80, 90, 100}, the median is <strong>80</strong>.<br>
For the incomes {20k, 22k, 25k, 30k, 200k}, the median is <strong>25k</strong>, which accurately reflects the “typical” income for
most individuals.
</div>

- **When useful**<br>
The median is a **robust statistic**: it is not influenced by extreme or erroneous values.<br>
It is therefore appropriate for **skewed** or **non-normal** distributions, such as **personal income**, **property
prices**, or **waiting times**.

- **Interpretation**<br>
While the mean provides an **arithmetic balance point**, the median represents a **positional balance**: 50% of the
observations lie below it and 50% above.<br>
This makes it more informative when the goal is to understand the **distribution of outcomes** rather than their
numerical magnitude.


## **The Mode**

The **mode** is the **most frequently occurring value** in a dataset.<br>
Unlike the mean and median, the mode can be applied to both **qualitative (categorical)** and **quantitative** data.

<div class="example-box">
<strong>Examples</strong><br>
For shoe sizes among students {42, 42, 43, 44, 42, 43}, the mode is <strong>42</strong>.<br>
For car colors in a parking lot {red, blue, blue, white, blue, black}, the mode is <strong>blue</strong>.
</div>

- **When useful**<br>
The mode is especially relevant when data are **categorical** or **discrete**, where arithmetic operations are
meaningless.<br>
It indicates the **most common or popular category**.<br>
In continuous data, the mode corresponds to the **peak of the probability density function**, representing the **most
probable value**.<br>

- **Limitations**<br>
A dataset may have **no mode** (if all values are unique), or **several modes** (bimodal or multimodal distributions).
In such cases, the mode signals that the population might contain **distinct subgroups** — useful information in
**marketing**, **biology**, or **sociology**.

## **The Geometric Mean**

The **geometric mean** is defined as:

<div class="formula-box"> G = (∏<sub>i=1</sub><sup>n</sup> x<sub>i</sub>)<sup>1/n</sup> </div>

It is suitable for **multiplicative** or **ratio-based** data, such as **growth rates**, **financial returns**, or
**environmental concentrations**.

<div class="example-box">
<strong>Example</strong><br>
Suppose an investment increases by 10%, 20%, and 30% in three consecutive years.<br>
The arithmetic mean rate of 20% <strong>overstates</strong> the actual performance.<br>
</div>

Using the geometric mean:<br>

<div class="formula-box">
    (1.10 &times; 1.20 &times; 1.30)<sup>1&#8260;3</sup> − 1 = 0.192 (19.2%)
</div>

This value correctly reflects the **compound annual growth rate**.<br>

- **When useful**<br>
The geometric mean is appropriate whenever the data are **proportional** or **scale-dependent**, and when changes occur
in **multiplicative** rather than **additive** form, such as **population growth**, **inflation**, or **interest
accumulation**.
It ensures that equal percentage increases and decreases offset each other correctly.

## **The Harmonic Mean**

The **harmonic mean** is calculated as:

<div class="formula-box"> H = n &frasl; (∑<sub>i=1</sub><sup>n</sup> 1&frasl;x<sub>i</sub>) </div>

It gives **greater weight to smaller values** and is therefore useful when dealing with **rates** or **ratios**.

<div class="example-box">
<strong>Example</strong><br>
A car travels 60 km at 60 km/h and another 60 km at 120 km/h.<br>
The average speed is not (60 + 120)/2 = 90 km/h, because the car spends more time at the slower speed.<br>
The correct average speed is: <br>

<div class="formula-box_2">H = 2 / ((1/60) + (1/120)) = 80 km/h</div>
</div>

This reflects the **true overall rate**.

- **When useful**<br>
The harmonic mean is ideal for situations involving **rates** (speed, density, efficiency) or **ratios** (e.g., P/E
ratios in finance), where the quantity being averaged is in the **denominator** of a relationship.

## **Weighted and Trimmed Means**

A **weighted mean** assigns **different levels of importance** to each observation. It is calculated as:

<div class="formula-box"> x&#772;<sub>w</sub> = (∑ w<sub>i</sub>x<sub>i</sub>) &frasl; (∑ w<sub>i</sub>) </div>

where
w<sub>i</sub> are the **weights**.

<div class="example-box">
<strong>Example</strong><br>
In a course where the midterm counts 40% and the final exam 60%, a student who scores 75 and 85 obtains:

<div class="formula-box_2">
(0.4×75) + (0.6×85) = 81
</div>
</div>

The weighted mean reflects the **greater importance** of the final exam.<br>

A **trimmed mean** is computed after removing a fixed percentage of the **smallest and largest observations** (e.g., 5%
or 10% on each side).<br>
It combines the **stability of the median** with the **efficiency of the mean**, and is often used in **official
statistics** (e.g., inflation measures) to **mitigate the influence of outliers**.

## **Choosing the Appropriate Measure**

The different **measures of location** are not interchangeable.
Each responds differently to the **shape of the distribution**, the **scale of measurement**, and the **purpose of the analysis**.

<table class="table_2-box">
    <thead>
        <tr>
            <th>Situation</th>
            <th>Recommended measure</th>
            <th>Reason</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Symmetric distribution, no outliers</td>
            <td>Arithmetic mean</td>
            <td>Reflects balance point and total magnitude</td>
        </tr>
        <tr>
            <td>Skewed data, outliers present</td>
            <td>Median</td>
            <td>Resistant to extreme values</td>
        </tr>
        <tr>
            <td>Categorical or nominal data</td>
            <td>Mode</td>
            <td>Identifies most frequent category</td>
        </tr>
        <tr>
            <td>Multiplicative growth or percentages</td>
            <td>Geometric mean</td>
            <td>Captures compound proportional change</td>
        </tr>
        <tr>
            <td>Rates, ratios, or speeds</td>
            <td>Harmonic mean</td>
            <td>Properly averages inverses</td>
        </tr>
        <tr>
            <td>Outliers but roughly symmetric</td>
            <td>Trimmed mean</td>
            <td>Balance between robustness and efficiency</td>
        </tr>
        <tr>
            <td>Different importance of observations</td>
            <td>Weighted mean</td>
            <td>Reflects relative significance</td>
        </tr>
    </tbody>
</table>

# **Measures of Dispersion**
While **measures of location** identify the **central point** of a dataset, they do not describe how widely the data are **spread** around that center.
Two datasets can have the same **mean** but very different **variability**.<br>
**Measures of dispersion** quantify this variability, providing essential information about the **stability**, **consistency**, and **reliability** of data.<br>

Dispersion measures are crucial in **descriptive statistics**, **quality control**, **risk analysis**, and **experimental design**, as they reveal how **concentrated** or **scattered** the observations are.<br>

## **Range**

The **range** is the simplest **measure of dispersion**.<br>
It is the difference between the **maximum** and **minimum** values in the dataset:

<div class="formula-box"> Range = max(x<sub>i</sub>) − min(x<sub>i</sub>) </div>

<div class="example-box">
<strong>Example</strong><br>
For the dataset {10, 12, 13, 15, 20},<br>
the range is 20 − 10 = <strong>10</strong>.<br>
</div>

- **Advantages**

  - Easy to compute and interpret.

  - Gives a quick sense of the total spread.

- **Limitations**

  - **Extremely sensitive** to outliers.

  - Does not reflect how the intermediate data are distributed.

## **Interquartile Range (IQR)**

The **interquartile range (IQR)** focuses on the **middle 50%** of the data and is defined as:

<div class="formula-box"> IQR = Q<sub>3</sub> − Q<sub>1</sub> </div>

where
Q<sub>1</sub> = **25th percentile (first quartile)**
Q<sub>3</sub> = **75th percentile (third quartile)**

<div class="example-box">
<strong>Example</strong><br>
For {10, 12, 13, 15, 20},<br>
Q<sub>1</sub> = 12, Q<sub>3</sub> = 15 → IQR = 15 − 12 = <strong>3</strong>.
</div>

- **Advantages**

  - **Robust** against outliers.

  - Represents the typical spread of the **central portion** of data.

  - Used in **boxplots** and **nonparametric summaries**.

- **Limitations**

  - Ignores the **outer 50%** of data.

  - Less efficient than **variance** for symmetric distributions.

## **Variance**

The **variance** measures the **average squared deviation** of observations from the **mean**:

<div class="formula-box"> s² = ∑(x<sub>i</sub> − x&#772;)² / (n − 1) </div>

Squaring the deviations ensures that both **positive** and **negative** differences contribute to the total variability.<br>

<div class="example-box">
<strong>Example</strong><br>
For {2, 4, 6, 8},<br>
x̄ = 5 → Variance = [(2−5)² + (4−5)² + (6−5)² + (8−5)²]/3 = <strong>6.67</strong>
</div>

- **Advantages**

  - Fundamental for **inferential statistics** and **ANOVA**.

  - Used to derive **standard deviation** and many **probabilistic models**.

  - Sensitive to all data points.

- **Limitations**

  - Expressed in **squared units** (e.g., m², €²), making interpretation less direct.

  - **Highly influenced** by outliers.

## **Standard Deviation**

The **standard deviation (s)** is the **square root** of the variance, restoring the same units as the data:

<div class="formula-box"> s = √s² = √(∑(x<sub>i</sub> − x&#772;)² / (n − 1)) </div>

<div class="example-box">
<strong>Example</strong><br>
Using the previous data: s = √6.67 = <strong>2.58</strong>.
</div>

- **Advantages**

  - Most widely used **measure of dispersion**.

  - Interpretable in the **same units** as the data.

  - Directly connected to the **normal distribution** (≈68% of data lie within ±1s of the mean).

- **Limitations**

  - Sensitive to **extreme values** and **skewness**.

  - Assumes that the **mean** is a meaningful central measure.

## **Coefficient of Variation (CV)**

The **coefficient of variation** expresses dispersion **relative to the mean**, as a percentage:

<div class="formula-box"> CV = (s / x&#772;) × 100% </div>

<div class="example-box">
<strong>Example</strong><br>
If x̄ = 50 and s = 5 → CV = (5/50)×100 = <strong>10%</strong>.
</div>

- **Advantages**

  - **Unit-free** measure, allowing comparison across variables with different scales.

  - Useful in **finance**, **economics**, and **reliability analysis**.

- **Limitations**

  - Not meaningful when the **mean** is near zero.

  - Requires data on a **ratio scale**.

## **Summary Table**

<table class="table_2-box">
  <thead>
    <tr>
      <th>Measure</th>
      <th>Formula</th>
      <th>Best for</th>
      <th>Strengths</th>
      <th>Limitations</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Range</strong></td>
      <td>max − min</td>
      <td>Quick overview of spread</td>
      <td>Simple, intuitive</td>
      <td>Distorted by outliers</td>
    </tr>
    <tr>
      <td><strong>IQR</strong></td>
      <td>Q<sub>3</sub> − Q<sub>1</sub></td>
      <td>Skewed or non-normal data</td>
      <td>Robust, focuses on central data</td>
      <td>Ignores tails of distribution</td>
    </tr>
    <tr>
      <td><strong>Variance</strong></td>
      <td>∑(x − x&#772;)² / (n − 1)</td>
      <td>Analytical and inferential use</td>
      <td>Mathematically powerful</td>
      <td>In squared units, less intuitive</td>
    </tr>
    <tr>
      <td><strong>Standard deviation</strong></td>
      <td>√(∑(x − x&#772;)² / (n − 1))</td>
      <td>Normal or near-normal data</td>
      <td>Widely used, easy to interpret</td>
      <td>Sensitive to outliers</td>
    </tr>
    <tr>
      <td><strong>Coefficient of variation</strong></td>
      <td>(s / x&#772;) × 100%</td>
      <td>Comparing variability across scales</td>
      <td>Unitless, versatile</td>
      <td>Not meaningful when mean ≈ 0</td>
    </tr>
  </tbody>
</table>


## **Conclusion**
Together, **measures of location** and **measures of dispersion** provide a complete description of a dataset.<br>
The **measures of location** (mean, median, mode, etc.) identify where the data tend to cluster, while the **measures of dispersion** (range, IQR, variance, standard deviation) indicate how tightly or loosely the data are spread around that center.<br><br>
A good statistical summary must always include **both**, since the same average can represent very different realities depending on the underlying variability.<br>
Understanding their **appropriate use**, **advantages**, and **limitations** allows researchers to make accurate interpretations and reliable comparisons across different contexts and datasets.

---