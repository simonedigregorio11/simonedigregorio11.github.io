---
title: "Homework 9"
layout: default
permalink: /homework9/
---

<link rel="stylesheet" href="/assets/css/pages/homework9.css">

[← Back to Home](/)

---

# **Interpretations of Probability, the Axiomatic Framework, and Measure-Theoretic Foundations**

## **Main Interpretations of Probability**

The concept of probability has several classical interpretations. The most influential are the **classical**, **frequentist**, **Bayesian**, and **geometric** interpretations. Each captures a different meaning of uncertainty and motivates the need for an axiomatic foundation.

### **1. The Classical Interpretation**
The classical interpretation defines probability through **symmetry and equiprobability**.<br>
If all outcomes are equally likely, then:

<div class="formula-box">
P(A) = |A| / |Ω|
</div>

This is suitable for games of chance but fails in cases with infinite or non-symmetric outcome spaces.

### **2. The Frequentist Interpretation**
The frequentist view defines probability as the **long-run relative frequency** of an event in repeated trials:

<div class="formula-box">
P(A) = lim <sub>(n→∞)</sub> N<sub>n</sub>(A) / n
</div>

Strengths: objective, grounded in empirical repetition.<br> 
Limitations: does not apply to **non-repeatable events**, and assumes independence without defining it.

### **3. The Bayesian Interpretation**

The Bayesian interpretation views probability as a **degree of rational belief**. <br>
Beliefs are updated through **Bayes' theorem**:

<div class="formula-box">
P(H | E) = P(E | H) P(H) / P(E)
</div>

It works for single events and is compatible with decision theory.<br>  
Main criticism: dependence on subjective priors.

### **4. The Geometric Interpretation**

This defines probability as a **uniform continuous measure** (length, area, volume).<br> 
For a point chosen uniformly from [a, b]:

<div class="formula-box">
P(A) = length(A) / length([a,b])
</div>

Although intuitive, it produces ambiguities (e.g., **Bertrand's paradox**) unless formalized with measure theory.

### **5. Need for a Unified Framework**

The diverse interpretations highlight inconsistencies.<br>
These are resolved by the **axiomatic approach**, which unifies all perspectives under a single rigorous system.

---

## **How the Axiomatic Approach Resolves Inconsistencies**

Kolmogorov (1933) defined a probability space as a triple:<br>

<div class="formula-box">
(Ω, F, P)
</div>

where:

- Ω is the sample space,
- F is a **σ-algebra**,
- P is a **probability measure**.

Kolmogorov’s axioms:

1. **Non-negativity:** P(A) ≥ 0  
2. **Normalization:** P(Ω) = 1  
3. **Countable additivity:** for disjoint A<sub>i</sub>,
<div class="formula-box">
   P(⋃ A<sub>i</sub>) = Σ P(A<sub>i</sub>)
</div>

### Why this solves earlier problems

- The **classical** interpretation is recovered as probability with a **uniform measure** on a finite set.

- The **frequentist** interpretation emerges from empirical frequencies converging to a measure satisfying the axioms (Law of Large Numbers).

- The **Bayesian** interpretation becomes coherent as subjective probabilities must satisfy the axioms to avoid **Dutch book** inconsistencies.

- The **geometric** interpretation is formalized through **Lebesgue measure**, eliminating paradoxes.

Thus, the axiomatic approach **unifies** all interpretations.

---

## 3. **Probability Theory and Measure Theory**

Modern probability is entirely based on **measure theory**.

### **1. σ-Algebras**
A σ-algebra F satisfies:

1. Ω ∈ F  
2. If A ∈ F, then A<sup>c</sup> ∈ F  
3. If A<sub>n</sub> ∈ F, then ⋃ A<sub>n</sub> ∈ F  

A σ-algebra determines which subsets are legitimate **events**.

### **2. Probability as a Measure**

A probability measure P is a measure with P(Ω) = 1.<br> 
This ties probability to integration and mathematical analysis.

### **3. Random Variables as Measurable Functions**

A random variable is a **measurable function**:

<div class="formula-box">
X : Ω → ℝ
</div>

such that for every Borel set B,

<div class="formula-box">
{ω : X(ω) ∈ B} ∈ F
</div>

### **4. Probability Distributions**

The distribution of X is the **pushforward measure**:

<div class="formula-box">
P<sub>X</sub>(B) = P(X ∈ B)
</div>

This connects abstract probability spaces with concrete distributions.

---

## **Consequences of the Axioms**

### **1. Subadditivity**

For any events A<sub>1</sub>, A<sub>2</sub>, ...

<div class="formula-box">
P(⋃ A<sub>i</sub>) ≤ Σ P(A<sub>i</sub>)
</div>

**Proof:**  
Define disjoint sets:

B<sub>1</sub> = A<sub>1</sub>  
B<sub>2</sub> = A<sub>2</sub> \ A<sub>1</sub>  
B<sub>3</sub> = A<sub>3</sub> \ (A<sub>1</sub> ∪ A<sub>2</sub>)  
...

Then:

<div class="formula-box">
⋃ A<sub>i</sub> = ⋃ B<sub>i</sub><br><br> 
P(⋃ A<sub>i</sub>) = Σ P(B<sub>i</sub>)
</div>

Since B<sub>i</sub> ⊆ A<sub>i</sub>, we have P(B<sub>i</sub>) ≤ P(A<sub>i</sub>).  

Thus:

<div class="formula-box">
P(⋃ A<sub>i</sub>) ≤ Σ P(A<sub>i</sub>)
</div>

---

### **2. Inclusion–Exclusion Principle**

### **For two events:**

<div class="formula-box">
P(A ∪ B) = P(A) + P(B) – P(A ∩ B)
</div>

**Derivation:**  

<div class="formula-box">
A ∪ B = A ∪ (B \ A)  
</div>

with disjoint sets. Thus,

<div class="formula-box">
P(A ∪ B) = P(A) + P(B \ A)
</div>

But 

<div class="formula-box">
P(B) = P(B \ A) + P(A ∩ B)
</div>

so

<div class="formula-box">
P(B \ A) = P(B) – P(A ∩ B)
</div>

Substituting yields the formula.

### **For three events:**

<div class="formula-box" id="threeEvents-formula">
P(A ∪ B ∪ C) = P(A) + P(B) + P(C) – P(A ∩ B) – P(A ∩ C) – P(B ∩ C) + P(A ∩ B ∩ C)
</div>

---

## **Conclusion**

The historical interpretations of probability capture different aspects of uncertainty but lack universal coherence. Kolmogorov’s **axiomatic approach** provides a rigorous and unified framework based on **measure theory**, where random variables are measurable functions and probability is a measure. Key results such as **subadditivity** and **inclusion–exclusion** follow naturally from the axioms.



---
