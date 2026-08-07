# Team Briefing — Choosing Our Research Design
### CO₂ Emissions Project | Prepared by Heesoo Kim (Team Lead)

---

## The Decision We Need to Make

We have two possible ways to structure this analysis:

| | **Design A — Time Series** | **Design B — Cross-Section** |
|---|---|---|
| **Unit of analysis** | One year = one row | One country = one row |
| **Question it answers** | "How did *Korea's* emissions change over time?" | "Why do *some countries* emit more than others?" |
| **Example** | Korea, 1911 → 2018 | 164 countries, all in 2018 |

**Recommendation: Design B (cross-section), with an optional Design C extension.**

Below is the evidence, so we can defend this choice if the professor asks.

---

## Why Design A Fails — Three Fatal Problems

We actually ran Design A on four countries to test it. The results were not close.

### Problem 1: Severe Autocorrelation → the independence assumption collapses

Linear regression requires that observations be **independent** of each other. In a time series, they are not: Korea's emissions in 2017 obviously predict Korea's emissions in 2018. The country doesn't reset each year.

We measured this with the **Durbin–Watson statistic**, which tests for autocorrelation:
- **DW ≈ 2.0** → no autocorrelation (assumption satisfied ✓)
- **DW < 1.0** → severe autocorrelation (assumption violated ✗)

| Design | Country | Durbin–Watson | Verdict |
|---|---|---|---|
| A (time series) | South Korea | **0.013** | Catastrophic |
| A (time series) | United States | **0.007** | Catastrophic |
| A (time series) | China | **0.059** | Catastrophic |
| A (time series) | Germany | **0.104** | Catastrophic |
| **B (cross-section)** | 164 countries, 2018 | **2.042** | **Clean ✓** |

A DW of 0.007 is about as bad as this statistic can get. Every standard error, t-statistic, p-value, and confidence interval produced by Design A would be **wrong** — specifically, they would be far too small, making weak results look highly significant.

> **How to say this in the presentation:** "Time-series data violates the independence assumption. Our Durbin–Watson statistics ranged from 0.007 to 0.104, where 2.0 indicates independence. This means the standard errors — and therefore every p-value — would be unreliable."

### Problem 2: Spurious Regression → we'd be measuring nothing but the calendar

In a single country, almost every variable grows over time: GDP grows, population grows, energy use grows, emissions grow. When two variables both trend upward, a regression will report a strong relationship **even if they are causally unrelated**. This is called **spurious regression**.

We tested this by regressing emissions on **time alone** — no economic variables at all, just the year number:

| Country | R² of log(CO₂) ~ GDP | R² of log(CO₂) ~ **TIME ALONE** |
|---|---|---|
| South Korea | 0.837 | **0.958** |
| China | 0.878 | **0.976** |
| United States | 0.902 | **0.862** |
| Germany | 0.752 | **0.778** |

For Korea and China, **the calendar year alone explains more variation than GDP does.** Any "finding" we reported from Design A would mostly be the statement "time passed and things got bigger" — which is not a research finding.

> **How to say this:** "In a single-country time series, a simple time trend explains 96–98% of the variation. Any predictor we add is largely capturing that shared trend rather than a genuine relationship. This is the classic spurious regression problem."

### Problem 3: We would only be able to study one country

Our research question is *"which factor most affects CO₂ emissions?"* — a question about **variation between different situations**. A single country gives us one economic system, one energy grid, one climate, one set of policies. We could not compare a coal-dependent economy against a hydro-powered one, because we would only have one of them.

Design B gives us **164 different national systems** to compare simultaneously.

---

## Why Design B Works

### 1. The independence assumption genuinely holds
Korea and Brazil are separate countries with separate economies. One row per country means one observation per unit. Our Durbin–Watson came out at **2.042** — essentially perfect.

### 2. It directly matches the research question
We are asking why emission levels *differ*. Design B is built from exactly that variation: 164 countries spanning from 0.01 to 10,354 million tonnes.

### 3. It follows the framework taught in class
The professor's linear-regression workflow — split the data, fit on train, diagnose residuals, check Cook's Distance, evaluate on test — assumes independent observations. Design B lets us apply that framework exactly as taught. Design A would force us to abandon half of it.

### 4. It has real explanatory power without cheating
Our cross-sectional STIRPAT model achieves **R² = 0.970 on training data and 0.969 on the held-out test set** — meaning it generalizes to countries it has never seen. That is a genuine result, not a trend artifact.

---

## Design C — The Best of Both (Optional Extension)

There is a third option that captures time **without** breaking independence: run Design B **separately for each year**, then compare the coefficients across years. Each individual regression is a clean cross-section, so no autocorrelation is introduced — but the sequence of coefficients reveals change over time.

We tested it, and it produces a clean, publishable-looking result:

| Year | n | CO₂–GDP elasticity | Durbin–Watson |
|---|---|---|---|
| 1990 | 163 | 1.128 | 1.80 |
| 2000 | 164 | 1.104 | 1.93 |
| 2010 | 164 | 1.076 | 1.98 |
| 2018 | 164 | **1.041** | 2.07 |

The elasticity declines **monotonically** from 1.128 to 1.041. Interpretation: in 1990, a 1% rise in GDP came with a 1.13% rise in emissions; by 2018 it came with only 1.04%. This is evidence of **relative decoupling** — economies are becoming more carbon-efficient — but because the value is still above 1.0, we have **not** achieved absolute decoupling. Growth still means more emissions, just slightly less than before.

Every Durbin–Watson stays near 2.0, so the independence assumption holds in every single regression.

> **How to say this:** "Rather than pooling years — which would violate independence — we estimated a separate cross-sectional model for each year and tracked how the coefficient evolved. This isolates temporal change while keeping every individual regression statistically valid."

---

## Summary Table for the Slide

| Criterion | Design A (Time Series) | **Design B (Cross-Section)** |
|---|---|---|
| Independence assumption | **Violated** (DW = 0.007–0.104) | Satisfied (DW = 2.04) |
| Spurious regression risk | **High** (time alone: R² up to 0.98) | Low (no time dimension) |
| Sample size | ~105 years, 1 country | 164 countries |
| Countries comparable | 1 | 164 |
| Matches course framework | Partially | **Fully** |
| Answers our question | No | **Yes** |

---

## Proposed Task Distribution (5 Members)

| Member | Role | Deliverable | Notebook Sections |
|---|---|---|---|
| **1** | **Data & Integrity Lead** | Load data, remove the 45 aggregate entities, run the circularity audit proving `co2 = coal + oil + gas + …`, document why ~60 of 79 variables were excluded | §1, §1a, §2 |
| **2** | **EDA & Visualization Lead** | Distributions, boxplots, skewness table (8.82 → 0.02), justify the log transform, build the presentation's headline scatter plot | §3, §4 |
| **3** | **Modeling Lead** | Train/test split, simple regressions, STIRPAT multiple regression, interpret elasticities against the Kaya identity | §5, §6, §7, §10 |
| **4** | **Diagnostics Lead** | Residual plot, Q-Q plot, Cook's Distance, leverage, VIF, robustness check after removing influential countries, test-set metrics | §8, §9, §11, §13 |
| **5** | **Interpretation & Policy Lead** | The per-capita reframing, the three importance measures, the coefficient identity discovery, limitations, and the decoupling extension (Design C) | §12, §14, §15 |



**Suggested integration checkpoint:** Members 1–2 must finish before 3–4 can begin, since modeling depends on the cleaned and log-transformed dataset. Member 5 can draft interpretation in parallel using preliminary results.

---

## Anticipated Question from the Professor

> *"Why didn't you use the time dimension? You have data from 1750."*

**Answer:** "We tested it. Single-country time series produced Durbin–Watson statistics between 0.007 and 0.104, indicating severe autocorrelation that would invalidate all our standard errors. We also found that a pure time trend alone explained up to 97.6% of the variation, meaning we would largely be measuring the calendar rather than any economic relationship. Instead, we used the time dimension in a way that preserves independence: we estimated separate cross-sectional models by year and compared the coefficients, which showed the CO₂–GDP elasticity declining from 1.128 in 1990 to 1.041 in 2018."
