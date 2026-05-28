# Willingness to Pay for Parking and Commuter Mode Choice: A Discrete Choice Analysis with Counterfactual Simulation

**Does informing drivers about the environmental or financial cost of car use change how much they are willing to pay for parking? And what actually determines whether someone drives, cycles, or walks to university?**

This project applies contingent valuation and discrete choice econometrics to survey data on commuter behavior at the University of Oldenburg. It combines willingness-to-pay (WTP) estimation across a randomized information experiment with a conditional logit model of transport mode choice — culminating in a counterfactual policy simulation.

📄 [Read the working paper](./wtp_discrete_choice.pdf)

---

## Key Findings

**1. Information framing barely moves WTP for parking**

| Group | Treatment | Mean WTP (€/hour) |
|-------|-----------|-------------------|
| A | Price information only (control) | €0.25 |
| B | Price + environmental cost of driving | €0.28 |
| C | Price + direct financial cost of driving | €0.15 |

Respondents across all three groups stated very low WTP. The majority refused to pay any parking fee at all. Environmental or cost framing produced no statistically significant difference — a null result with a clear policy implication: **information campaigns alone are unlikely to shift parking demand.**

**2. Distance is the dominant driver of mode choice**

A 1% increase in commuting distance raises the probability of choosing a car by 0.72% (own-elasticity) and reduces the probability of walking by 29.23%. Women are more likely than men to cycle or walk at any given distance.

**3. The most effective lever is proximity, not pricing**

A counterfactual simulation shows that relocating students who live beyond 5km to a hypothetical student home 5km from campus reduces the predicted probability of car commuting from **36% to 18%** — a halving of car dependence that no pricing or information intervention achieved.

---

## Methods

### Willingness to Pay — Contingent Valuation

Respondents who use a car as their primary or secondary transport were presented with a series of binary accept/reject questions at different price points (€0.25 – €2.25/hour). The **WTP** variable is defined as the highest price at which the respondent accepted:

```math
WTP_i = \max \{ p : \text{response}_{i,p} = \text{Yes} \}
```

The three randomization groups (A, B, C) were assigned before the survey to test whether providing environmental or cost information shifts reservation prices. WTP differences across groups are analyzed via OLS with HC1 heteroskedasticity-robust standard errors across five model specifications.

### OLS with Robust Standard Errors

Five models are estimated, progressively adding gender, student status, child dummy, age, income, and randomization group dummies:

```math
WTP_i = \alpha + \beta_1 \cdot \text{gender}_i + \beta_2 \cdot \text{student}_i + \beta_3 \cdot \text{child}_i + \beta_4 \cdot \text{age}_i + \beta_5 \cdot \text{income}_i + \beta_6 \cdot \text{group}_i + \varepsilon_i
```

Robust standard errors (HC1) correct for heteroskedasticity — necessary here because WTP responses are highly skewed with a spike at zero.

### Conditional Logit Model — Discrete Mode Choice

The primary transport choice (Car, Bike/E-Bike, Bus, Train, Walking, Motor Scooter) is modeled as a **conditional logit (McFadden, 1974)**. Each individual $i$ is assumed to choose the alternative $j$ that maximizes utility:

```math
U_{ij} = V_{ij} + \varepsilon_{ij}
```

where $\varepsilon_{ij}$ follows an i.i.d. Type I Extreme Value distribution. This yields closed-form choice probabilities:

```math
P_{ij} = \frac{\exp(V_{ij})}{\sum_{k} \exp(V_{ik})}
```

The systematic utility for the **Car** alternative is:

```math
V_{i,\text{car}} = \alpha_{\text{car}} + \beta_{\text{gender}} \cdot \text{gender}_i + \beta_{\text{age}} \cdot \text{age}_i + \beta_{\text{student}} \cdot \text{student}_i + \beta_{\text{inc}} \cdot \text{income}_i + \beta_{\text{child}} \cdot \text{child}_i + \beta_{\text{dist}} \cdot d_i
```

Individual-specific covariates (gender, age, income, etc.) enter with alternative-specific coefficients; $d_i$ is the distance from home to university.

### Marginal Effects

For a continuous variable $x$, the marginal effect on the probability of choosing alternative $j$ is:

```math
\frac{\partial P_{ij}}{\partial x_i} = P_{ij} \left( \beta_{j,x} - \sum_k P_{ik} \cdot \beta_{k,x} \right)
```

All marginal effects are evaluated at the sample means.

### Own- and Cross-Elasticities

The elasticity of choosing alternative $j$ with respect to a change in variable $x_m$ (associated with alternative $m$) is:

```math
E_{jm} = \frac{\partial P_{ij}}{\partial x_m} \cdot \frac{x_m}{P_{ij}}
```

When $j = m$ this is the **own-elasticity**; when $j \neq m$ it is a **cross-elasticity**. All elasticities are computed with respect to commuting distance.

### Independence of Irrelevant Alternatives (IIA)

A known limitation of the conditional logit is the **IIA property**: the ratio of probabilities between any two alternatives is unaffected by the presence or absence of a third alternative. In practice, removing Bus from the choice set barely changes the Car/Bike odds ratio — but in reality, Bus removal should disproportionately attract cyclists and walkers, not shift all modes equally. This illustrates that the logit model imposes implausibly rigid substitution patterns.

### Counterfactual Policy Simulation

For respondents with $d_i > 5$ km, the distance variable is set to 5 km and predicted probabilities are recomputed using the estimated model. The difference in average predicted car probability between the baseline and the counterfactual identifies the pure distance effect, holding all other characteristics constant.

---

## Data

| Property | Value |
|----------|-------|
| Source | Survey administered at University of Oldenburg (LimeSurvey) |
| Respondents | ~500 (students and employees) |
| Transport modes | Car, Bike/E-Bike, Bus, Train, Walking, Motor Scooter |
| Randomization | 3 groups (A: control, B: environmental info, C: cost info) |
| WTP sample | Car users only (primary or secondary transport) |
| Key variables | Distance to campus, distance to bus/train stop, gender, age, income, student status, child dummy |

> **Sample characteristics:** 67.8% female, 77.8% students, mean age 28.8 years — younger and more female than the general population. Results should be interpreted in this context.

---

## Repository Structure

```
├── EmpiricalProject2025.Rmd    # Full analysis (R Markdown)
├── raw_data.csv                # Data is not included in this repository. It was collected via a university survey at the University of Oldenburg. Available upon request with appropriate permissions.
└── README.md
```

---

## How to Run

```r
# Install required packages
install.packages(c("mlogit", "tidyverse", "modelsummary", "dfidx",
                   "knitr", "kableExtra", "ggplot2", "sandwich"))

# Knit to HTML or PDF in RStudio, or run chunks interactively
```

> The analysis was developed in RStudio. Data is not included in this repository. It was collected via a university survey at the University of Oldenburg. Available upon request with appropriate permissions.. All preprocessing steps are self-contained in the .Rmd file.

---

Uzun, B. (2025). *Willingness to Pay for Parking and Commuter Mode Choice: A
Discrete Choice Analysis with Counterfactual Simulation. Applied Environmental Economics, University of Oldenburg.
