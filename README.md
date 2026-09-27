# District Cooling Service-Risk Analytics

### Identifying when customer differential pressure becomes vulnerable — and what operations should watch before service falls below target

**Python · statsmodels · scikit-learn · SHAP · Power BI**

A district cooling network needs to maintain adequate customer differential pressure (DP). This project examines the operating conditions associated with DP risk and translates the findings into a practical monitoring view.

The core question:

> **When does customer DP become vulnerable, which signals move with that risk, and what should operations monitor before the 12 PSI target is crossed?**

<p align="center">
  <img src="result_images/dashboard.jpg" width="900" alt="District cooling operations dashboard"/>
</p>

## Results at a glance

| Finding | Evidence | Why it matters |
|---|---:|---|
| Peak-period breach risk | **37.9% vs 15.4%** | Risk is materially higher during the peak operating window |
| Peak window | **13:00–17:00** | Focus readiness and monitoring where vulnerability is concentrated |
| Weather relationship | Humidex–DP **r = -0.74** | Hotter/more humid conditions provide useful risk context |
| Demand relationship | Flow–DP **r = -0.68** | Rising network demand is part of the pressure-risk picture |
| Multivariable model | **R² ≈ 0.79** | The observed operating variables explain a substantial share of DP variation |

The dataset contains **4,032 time-series observations** at 5-minute cadence, joined with hourly weather data.

## 1. The risk is not evenly distributed through the day

The strongest business signal is the concentration of DP vulnerability during the afternoon operating window.

<p align="center">
  <img src="result_images/daily%20psi%20slide%203.png" width="860" alt="Customer differential pressure over time"/>
</p>

A simple average can hide that pattern. Breaking the day into operating windows shows where service risk deserves more attention.

The validated like-for-like comparison is:

- **Peak period:** 37.9% of observations below 12 PSI
- **Outside peak:** 15.4% below 12 PSI
- Peak-period breach risk is therefore roughly **2.5×** the outside-peak rate

That makes the 13:00–17:00 period a practical monitoring priority.

## 2. Demand conditions line up with the risk window

Cooling demand and DP behaviour change together across the day.

<p align="center">
  <img src="result_images/dp%20frequency%20and%20cooling%20demand%20by%20hour.png" width="820" alt="DP frequency and cooling demand by hour"/>
</p>

This does not prove that time of day itself causes a breach. It shows that the higher-risk period coincides with a different operating regime—higher network demand, different flow conditions, weather load, and plant dispatch.

That distinction matters because operational action should target the system conditions, not the clock.

## 3. Weather is useful context, not a causal claim

Humidex is strongly negatively associated with customer DP in the observed period.

<p align="center">
  <img src="result_images/dp%20response%20across%20humidex%20slide%206.png" width="820" alt="DP response across humidex range"/>
</p>

The observed Humidex–DP correlation is approximately **-0.74**.

Weather cannot be controlled, but it can improve readiness. Higher Humidex combined with rising demand can justify closer monitoring before the DP margin becomes critical.

The analysis treats weather as a **contextual early-warning signal**, not proof that Humidex directly causes a particular DP response.

## 4. Multivariable analysis separates overlapping relationships

Pairwise correlations are useful for exploration, but plant loading, flow, weather, demand, and pressure can move together.

A multivariable regression was therefore used to estimate adjusted relationships while controlling for the other observed variables. HAC robust standard errors were used to reduce sensitivity to heteroskedasticity and serial correlation in the time-series residuals.

The fitted model explains approximately **79% of observed DP variation (R² ≈ 0.79)**.

Raw regression coefficients are **not used to rank variables** when their units and scales differ. A larger coefficient does not automatically mean a variable is the stronger operational driver.

## 5. SHAP provides a second model-behaviour check

SHAP was used as an additional interpretability lens to examine which features most influenced the fitted model's predictions and whether the directions broadly aligned with the statistical analysis.

<p align="center">
  <img src="result_images/shap%20confirmation.png" width="820" alt="SHAP model interpretation"/>
</p>

SHAP explains the fitted model. It does **not** convert observational data into causal evidence.

## Operational interpretation

The analysis supports a simple monitoring framework:

1. Track customer DP against the 12 PSI target.
2. Give the **13:00–17:00** window more operational attention.
3. Review DP together with flow, demand, weather, output pressure, and plant dispatch.
4. Investigate deterioration before the threshold is crossed.
5. Validate any control or setpoint change with engineering.

A useful operating view is therefore not a single KPI. It is the combination:

~~~text
Customer DP
   +
Demand / Flow
   +
Weather context
   +
Plant loading / output conditions
   ↓
Risk review
   ↓
Engineering-validated action
~~~

## Why the plant coefficients require care

Plant loading can be endogenous: a plant may start or increase output **because** system conditions are already deteriorating.

That means a raw or adjusted plant coefficient can reflect both the plant's operating effect and the dispatch decision that put the plant into service.

For that reason, the analysis does not claim that a plant coefficient proves hydraulic causation. Confirming the mechanism would require additional information such as plant sequencing, operating logs, and network topology.

## Analytical workflow

~~~mermaid
flowchart LR
    A[5-minute plant telemetry] --> C[Validation & feature engineering]
    B[Hourly weather] --> C
    C --> D[Time / threshold analysis]
    D --> E[Correlation & multivariable regression]
    E --> F[Interactions & SHAP]
    F --> G[Risk conditions]
    G --> H[Power BI operating view]
~~~

## Limitations

- The analysis is observational and does not establish causation.
- The available period may not represent every season or operating regime.
- Plant dispatch may respond to the same demand conditions that affect DP.
- Model coefficients depend on variable units, scaling, specification, and observed range.
- A live early-warning system would require prospective validation and explicit alert-performance testing.
- Engineering review is required before changing plant controls or operating setpoints.

## Repository contents

~~~text
data/                 Public analysis dataset
result_images/        Published analysis and dashboard visuals
README.md             Business and technical narrative
~~~

The public repository is intentionally limited to the analysis data and presentation-safe evidence. Private source materials, local metadata, and non-reproducible development artifacts are excluded from the current public version.

---

**What this repository demonstrates:** operational analytics, time-series investigation, statistical modeling, robust inference, model explainability, Power BI communication, and disciplined separation of evidence from causal interpretation.
