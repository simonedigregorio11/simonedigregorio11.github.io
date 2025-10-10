---
title: "Homework 1"
layout: default
permalink: /homework1/
---

[← Torna alla Home](/)

---

# **Homework 1**

## **What is Statistics?**

Statistics is the branch of mathematics concerned with collecting, organizing, analyzing, interpreting, and presenting data. It provides tools to deal with uncertainty, variability, and inference from samples to populations. It involves methods for:
- **Descriptive statistics:** summarizing data using measures like mean, median, variance, histograms, frequency distributions.

- **Inferential statistics:** drawing conclusions about a larger population from a sample (hypothesis testing, confidence intervals, regression, Bayesian inference).

- **Probabilistic modeling:** modeling uncertain processes with statistical distributions (normal, Poisson, binomial, heavy-tailed, etc.).

- **Advanced techniques:** simulation (e.g. Monte Carlo), time-series analysis, multivariate statistics, robust methods, Bayesian networks, and more.

By applying statistical tools, one can detect patterns, estimate probabilities, test hypotheses, quantify uncertainty, and support decision-making under risk.

## **The Relevance of Statistics to Cybersecurity**

Cybersecurity is the field that protects systems, networks, devices, and data from attacks, intrusions, or unauthorized use. In a world where digital infrastructures generate enormous amounts of data (logs, network traffic, security events, user activity, sensor signals), statistics become a fundamental ally. Below are the main ways in which statistics is useful in cybersecurity.

### **1. Anomaly and Intrusion Detection** 

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
One of the most direct applications of statistics in cybersecurity is anomaly detection, identifying patterns that deviate significantly from normal behavior. By building statistical models of network traffic or system usage, analysts can flag unusual events that may indicate an intrusion. For example, a sudden spike in failed logins or data transfers can be detected using statistical thresholds or distance metrics such as the Mahalanobis distance.
Advanced systems now combine statistical analysis with machine learning to enhance accuracy and reduce false alarms. This approach underpins User and Entity Behavior Analytics (UEBA), where models learn what “normal” looks like and automatically alert when behavior changes in a statistically significant way.
</div>

### **2. Quantifying Cyber Risk**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Another critical role of statistics is quantifying cyber risk, measuring the likelihood and potential impact of security incidents. Instead of relying on vague categories like “high” or “low” risk, statistical models assign probabilities and expected losses to different threat scenarios.
The National Cyber Security Centre (NCSC, 2024) emphasizes that probabilistic modeling allows organizations to make evidence-based decisions about which risks to mitigate first. Techniques such as Bayesian inference and Monte Carlo simulation make it possible to estimate how likely an attack is and what its financial impact could be.
</div>

### **3. Optimizing Cybersecurity Investments**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Statistics also supports decision-making about how much to invest in security measures. The Gordon–Loeb Model (Gordon & Loeb, 2002) uses statistical and economic analysis to calculate the optimal level of cybersecurity investment. It shows that spending should be proportional to the expected loss from an attack, but not excessively high, usually no more than 37% of the potential loss.
This quantitative reasoning helps organizations allocate resources effectively and avoid overspending or underprotecting critical systems.
</div>

### **4. Supporting Data-Driven Strategy**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Statistical thinking enables cybersecurity professionals to move from intuition to data-driven strategy. By analyzing large datasets and using predictive models, leaders can make better-informed decisions about policies, defenses, and future risks.
According to McKinsey & Company (2023), the integration of statistical analysis into cybersecurity transforms it from a purely technical issue into a strategic discipline. Companies that use quantitative metrics can prioritize actions, predict vulnerabilities, and demonstrate the business value of security programs.
</div>

### **5. Measuring Resilience and Recovery**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Beyond prevention, cybersecurity also involves resilience, the ability of systems to recover after an attack. Statistics provides models for measuring how quickly and effectively a network can return to normal operations.
A recent study in Reliability Engineering and System Safety (ScienceDirect, 2025) applies statistical reliability models to cybersecurity, quantifying uptime, failure probability, and recovery time. These models help organizations understand their true resilience and prepare better disaster recovery plans.
</div>

## **Statistical Tools and Methods Used in Cybersecurity**

Cybersecurity professionals rely on a variety of statistical tools and techniques to analyze data, identify patterns, and predict threats. These methods range from basic descriptive statistics to advanced probabilistic and inferential models. Each of them serves a specific purpose in detecting anomalies, assessing risks, and supporting decision-making.

### **1. Descriptive Statistics**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
  Descriptive statistics, such as mean, median, variance, and standard deviation, summarize large volumes of security data, like network activity or login attempts. These measures help define what “normal” behavior looks like within a system. Once normal baselines are established, deviations can be detected as potential anomalies.
</div>

### **2. Probability Distributions**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Statistical distributions such as the Normal, Poisson, and Exponential distributions are used to model the frequency and timing of cyber events. For instance, the Poisson distribution can estimate how often intrusion attempts occur in a given time frame, while the Exponential distribution models the expected time between attacks.
</div>

### **3. Regression Analysis**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Linear and logistic regression are used to find relationships between variables, for example, correlating the number of phishing emails with the likelihood of a successful breach. Logistic regression can also predict binary outcomes, such as whether an event represents a legitimate user or a potential attacker.
</div>

### **4. Bayesian Inference**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Bayesian statistics are especially powerful in cybersecurity because they allow analysts to update risk estimates as new data becomes available. For example, if a certain type of attack becomes more frequent, Bayesian models can automatically adjust the probability of future occurrences.
</div>

### **5. Clustering and Outliner Detection**

<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Clustering algorithms such as k-means or DBSCAN use statistical distance measures to group similar network behaviors and isolate anomalies. Outlier detection helps uncover unusual activities, like a user downloading far more data than usual, that could signal data exfiltration or insider threats.
</div>

### **6. Time-Series Analysis**
<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Cyber events often occur over time, making time-series analysis crucial. By applying methods such as autoregressive (AR) models or moving averages, analysts can detect periodic patterns, monitor trends, and forecast future incidents. Time-based modeling helps identify subtle shifts in attack frequency or system performance.
</div>

### **7. Monte Carlo Simulation**
<div style="border: 1px solid #b5e853; padding: 10px; margin: 30px; background: url('../images/blacktocat.png'); border-radius: 5px;">
Monte Carlo simulations allow cybersecurity teams to model thousands of possible attack scenarios using random sampling. These simulations estimate the probability distribution of potential outcomes, such as financial losses or system downtimes, providing a realistic view of uncertainty and risk.
</div>





## ***References***

- Gordon, L. A., & Loeb, M. P. (2002). The economics of information security investment. ACM Transactions on Information and System Security.

- McKinsey & Company. (2023). Cyber risk measurement and the holistic cybersecurity approach.

- National Cyber Security Centre (NCSC). (2024). Introducing cyber risk quantification.

- National Institute of Standards and Technology (NIST). (2008). Special Publication 800-55 Rev.1: Performance measurement guide for information security.

- ScienceDirect. (2025). Statistical models for measuring cyber resilience. Reliability Engineering and System Safety Journal.

---

[← Torna alla Home](/)