# Team Briefing — Choosing Our Research Question
### CO₂ Emissions Project | Prepared by Heesoo Kim

**Purpose of this document:** Before we divide up the work, we need to agree on *what question we are actually
asking*. This briefing explains why our initial instinct — "which variable has the highest R?" — is a weak research
question, presents four stronger alternatives that we have already tested against the data, and gives an honest
assessment of what this dataset can and cannot support.

---

## Part 1 — Why "Which Factor Has the Biggest R?" Is a Weak Question

This is the most natural first instinct, and it is the one we should move away from. Three reasons.

### Reason 1: The answer is predetermined by definitions, not by nature

We ran every candidate predictor against total CO₂ (2018, log-log, n = 128 training countries). Here is what wins:

| Predictor | R² | What it actually tells us |
|---|---|---|
| `primary_energy_consumption` | **0.963** | Burning fuel produces CO₂ — this is **chemistry**, not a finding |
| `gdp` | **0.922** | Economic activity *is* energy consumption — nearly an identity |
| `methane` | 0.577 | Another emission, not a cause |
| `population` | 0.486 | Bigger countries emit more — near-tautological |
| `energy_per_gdp` | 0.132 | Actually informative, but ranks last |

Notice the pattern: **the higher the R², the less we learn.** The winner is a physical law. Reporting "energy
consumption best predicts CO₂" is equivalent to reporting "wet things best predict water."

### Reason 2: The dataset is full of accounting identities that produce fake R²

This is the single most important methodological point in our project. In this dataset:

```
co2 = coal_co2 + oil_co2 + gas_co2 + cement_co2 + flaring_co2 + other_co2
```

We verified this numerically: the median difference between `co2` and the sum of its components is
**0.000000%**. They are the same number.

So if a team member regresses `co2 ~ coal_co2`, they will get an impressive R² — and it will mean **nothing**,
because `coal_co2` is not a *cause* of CO₂, it is a *portion* of CO₂.

> **Analogy for the presentation:** "What best predicts how much money is in my wallet? The number of $20 bills —
> R² = 0.95!" That is not a discovery. It is addition. The bills aren't a cause of the money; they *are* the money.

Roughly **60 of the 79 columns** in this dataset fall into this trap: fuel components, per-capita ratios,
cumulative sums, global shares, alternative accounting versions, and downstream consequences like
`temperature_change_from_co2`. A question phrased as "which variable has the highest R" actively rewards
picking these, because circular variables always win.

### Reason 3: R measures association strength, not importance

Even setting aside circularity, a high R does not mean a variable is *important* in any decision-relevant sense.
Population is the strongest driver of total emissions, but "reduce the population" is not a conclusion anyone can
act on. A useful research question has to ask something whose answer could change what someone does.

### What a good question looks like instead

| Weak question | Strong question |
|---|---|
| Which variable has the highest R? | Has the relationship **changed over time**? |
| What correlates with CO₂? | Which countries **deviate** from what their income predicts, and why? |
| Which predictor is best? | Does the relationship **bend** at high income, as theory claims? |
| — | Does the answer **change** when we reframe the outcome variable? |

Every question on the right has an answer we cannot guess in advance. That is the test.

---

## Part 2 — Four Candidate Research Topics (All Pre-Tested on the Data)

We ran each of these against the real data before recommending them. Results below are actual output, not estimates.

### 🥇 Option 1 — Decoupling: "Is economic growth becoming less carbon-intensive?"

**Design:** Estimate a separate cross-sectional regression for each year, then track how the CO₂–GDP elasticity
evolves. This uses the time dimension **without** violating the independence assumption, because each individual
regression is a clean cross-section.

**Actual results:**

| Year | n | CO₂–GDP elasticity | Durbin–Watson |
|---|---|---|---|
| 1990 | 163 | 1.128 | 1.80 |
| 1995 | 164 | 1.092 | — |
| 2000 | 164 | 1.104 | 1.93 |
| 2005 | 164 | 1.096 | — |
| 2010 | 164 | 1.076 | 1.98 |
| 2015 | 164 | 1.058 | — |
| 2018 | 164 | **1.041** | 2.07 |

**Finding:** The elasticity declines **monotonically** from 1.128 to 1.041. In 1990, a 1% rise in GDP came with a
1.13% rise in emissions; by 2018 it came with only 1.04%.

**Why this is our strongest option:**
- The answer was not knowable in advance — we had to actually estimate it
- The nuance is genuinely interesting: this is **relative decoupling**, but because the elasticity is still above
  1.0, the world has **not** achieved **absolute decoupling**. Growth still means more emissions, just less than before
- Every Durbin–Watson stays near 2.0, so the independence assumption holds in every single regression
- Direct policy relevance
- It is a question about *change*, not about *ranking*

### 🥈 Option 2 — Residual Analysis: "Which countries outperform their income, and why?"

**Design:** Fit the regression, then study the **residuals** rather than the coefficients. The regression line
becomes a *benchmark* — "given your income level, this is the expected emission level" — and the residual measures
how far a country deviates from that expectation.

**Actual results (2018, per-capita CO₂ vs per-capita GDP):**

| Efficiency leaders (far below the line) | Efficiency laggards (far above the line) |
|---|---|
| Switzerland, Norway, Sweden | Mongolia, Kazakhstan, Trinidad & Tobago |
| Rwanda, Uganda, Sri Lanka | South Africa, Zimbabwe, China |

**Why this is strong:** It reframes regression from a *prediction tool* into a *benchmarking tool*. Norway and
Switzerland are wealthy yet emit far below expectation — because of hydro and nuclear generation. That is a
concrete, explainable policy story, and it is exactly the kind of insight a pure R-ranking would never surface.

### 🥉 Option 3 — Environmental Kuznets Curve: "Do emissions eventually fall as countries get richer?"

**Design:** Add a squared income term. If emissions eventually decline with wealth, the quadratic coefficient
should be negative and there should be a turning point *inside the observed data range*.

**Actual results:**

```
Quadratic coefficient = -0.0978,  p = 0.0006   ← statistically significant!
Implied turning point = $2,834,861 GDP per capita
```

**The catch:** The highest observed income in our data is Qatar at roughly **$145,000** per capita. The estimated
turning point sits **20× beyond any country that exists.**

**Why this is valuable despite being a null result:** It is a textbook demonstration that **statistical significance
is not the same as substantive meaning**. The p-value of 0.0006 looks compelling, but the model is extrapolating far
outside the data range, where it has no evidence at all. Our honest conclusion: the data show *diminishing returns*
to income, but **no evidence of an actual turning point within the observed range.**

This makes an excellent secondary section — it shows methodological maturity to report and correctly interpret a
negative result.

### ❌ Option 4 (Not Recommended) — Carbon Leakage via Consumption-Based Emissions

**The idea:** Wealthy countries may appear clean because they have offshored manufacturing. The dataset has
`consumption_co2` and `trade_co2` to test this.

**Why we should skip it:**
- Only **119 countries** have `consumption_co2`, versus 213 for production-based `co2` — we would lose nearly half our sample
- The correlation between income and net-import share came out at **−0.04**, essentially zero
- The measure is unstable for small economies (Malta shows +568%, which is a measurement artifact, not a finding)

Attractive topic, insufficient data. We should mention it only in the Limitations section.

---

## Part 3 — Is This Dataset Appropriate for Undergraduate Research?

Short answer: **Yes for a methods course project. No for original research.** Here is the honest breakdown.

### Strengths

| Strength | Why it matters for us |
|---|---|
| **Authoritative and well-documented** | Our World in Data is a credible, citable source. No one will question the data provenance |
| **Already clean** | 48,058 rows with consistent formatting. We spend our time on analysis, not on fixing broken CSVs |
| **Right size** | Large enough to be credible, small enough to run on a laptop |
| **Contains a real methodological trap** | The circularity problem is a genuine trap. *Finding it and avoiding it is itself a demonstration of skill* — this may be the most impressive thing in our presentation |
| **Design flexibility** | Supports cross-section, repeated cross-section, and time-series designs, so we can justify our choice |
| **Zero motivation cost** | Nobody needs convincing that climate emissions matter |

### Weaknesses

| Weakness | Implication for us |
|---|---|
| **Only ~6–8 genuinely independent predictors** | Of 79 columns, most are CO₂ rewritten. Our effective variable space is small |
| **No causal identification** | There is no policy shock, natural experiment, or instrument. We can measure association only — we must say so explicitly |
| **Heavily studied territory** | STIRPAT and EKC have thousands of published papers. We will not produce a novel finding, and we should not claim to |
| **Key covariates missing** | No climate/heating-degree data, no electricity generation mix, no industrial composition, no carbon-pricing indicator |
| **GDP ends at 2018** | Anything requiring income data cannot use the most recent years |
| **Non-random missingness** | 59 countries lack GDP — mostly small states and conflict-affected countries. Our sample is not a random draw from all countries |

### The verdict

**For a regression methods course:** this dataset is close to ideal. The grading criterion is not *"did you discover
something new?"* but *"can you specify, estimate, diagnose, and interpret a regression correctly?"* This dataset lets
us demonstrate every step of that workflow — and the circularity trap gives us something to demonstrate *judgment*
about, which is rarer and more impressive than technical execution.

**For an independent research project or thesis:** this dataset alone is insufficient. To make a genuine
contribution we would need to merge in external data — electricity generation mix, carbon pricing adoption dates,
climate data — to create variation the OWID file does not contain.

**What this means for how we present:** We should be upfront that our contribution is **methodological rigor
applied to a well-understood question**, not a new discovery. Framing it that way is honest and will read as
mature. Overclaiming novelty is the fastest way to lose credibility in Q&A.

---

## Part 4 — Recommended Structure

We propose keeping the cross-sectional STIRPAT analysis as the core, and adding the decoupling extension:

| # | Section | Purpose | Source |
|---|---|---|---|
| 1 | **The circularity trap** | Open by showing why we discarded ~60 of 79 variables despite their R² near 0.99 | Part 1, Reason 2 |
| 2 | **Cross-sectional model** | STIRPAT: population, income, energy intensity. Train R² = 0.967, Test R² = 0.969 | Existing notebook |
| 3 | **The reframing** | Total emissions → population dominates. Per-capita emissions → income dominates. Same data, different question, different answer | Existing notebook §14 |
| 4 | **Decoupling over time** | Elasticity falls 1.128 → 1.041, showing relative but not absolute decoupling | Option 1 |
| 5 | **EKC test** | Quadratic term significant but turning point lies outside the data range — significance ≠ meaning | Option 3 |
| 6 | **Limitations** | No causality, production-based accounting, missing covariates, non-random missingness | Part 3 |

This arc moves from *"here is a trap we avoided"* → *"here is our model"* → *"here is why the framing matters"* →
*"here is how it is changing"* → *"here is what we could not establish."* That is a complete research narrative, and
none of it depends on the weak question we started with.

---

## Discussion Points for Our Next Meeting

1. Do we all agree to drop "which variable has the highest R" as the framing?
2. Do we include both the decoupling extension (§4) and the EKC test (§5), or only one, given our time limit?
3. Who takes ownership of the limitations section? It is the part most likely to be tested in Q&A.
4. Should we attempt to merge external data (electricity mix, carbon pricing), or stay within OWID and be explicit
   about the resulting constraints?
