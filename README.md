# District Cooling Service-Risk Analytics

Operational analysis of customer differential-pressure risk using plant telemetry, weather conditions, statistical modeling, and Power BI.

## Business question

A district cooling network needs to maintain customer differential pressure (DP) at or above **12 PSI**. The analysis asks:

> **When does DP risk increase, which operating conditions move with that risk, and what should operations monitor before service falls below target?**

The dataset contains 5-minute plant telemetry joined with hourly weather observations. The analysis focuses on the afternoon period, where pressure risk is concentrated.

## Analysis

The workflow combines:

- data-quality and exploratory analysis;
- peak vs. non-peak comparisons;
- multivariable regression with robust standard errors;
- interaction analysis;
- SHAP-based model interpretation; and
- a Power BI operations view.

```text
Plant telemetry + weather
          ↓
Data quality / feature engineering
          ↓
Peak-period risk analysis
          ↓
Multivariable driver analysis
          ↓
Model interpretation
          ↓
Operational monitoring
```

## Key findings

- DP risk is concentrated during the **13:00–17:00** operating window.
- In the validated peak/non-peak comparison, **37.9% of peak observations were below 12 PSI versus 15.4% outside the peak window**.
- Humidex and system flow move negatively with DP in the observed data, making them useful demand-context indicators.
- Output pressure has a positive adjusted relationship with customer DP after controlling for the other modeled variables.
- Plant 1 and Plant 2 coefficients are interpreted in operating context rather than as isolated causal effects; plant dispatch can respond to the same system conditions that affect DP.

The regression and SHAP results are used as **diagnostic evidence**, not proof of physical causation. Any change to plant controls or operating setpoints requires engineering validation.

## How to read the model

Raw regression coefficients are not used to rank variables unless their units and scales are comparable. A larger coefficient does not automatically mean a variable is the more important operational driver.

For example, plant output and pressure variables are measured on different scales and can also reflect dispatch decisions made in response to system load. The analysis therefore uses the model to separate overlapping relationships and identify conditions worth operational attention rather than to claim a single causal driver.

## Operational use

The analysis supports a simple monitoring approach:

1. Track customer DP against the 12 PSI target.
2. Watch the 13:00–17:00 period more closely.
3. Monitor output pressure, system flow, weather load and plant dispatch together.
4. Investigate deterioration before the customer threshold is crossed.
5. Validate any proposed control change with engineering and operations teams.

## Repository structure

```text
notebooks/       Reproducible analysis notebooks
src/             Reusable analysis code, where applicable
data/            Analysis inputs suitable for public use
dashboard/       Power BI artifact(s)
result_images/   Exported analytical visuals
docs/            Supporting documentation
```

## Reproducibility

The public version of this project should use repository-relative paths and neutral field names. It should not depend on local Windows paths, deleted source files, employer/customer identifiers, or private case materials.

A clean run should reproduce the headline metrics and figures used in this README. If a metric cannot be reproduced from the public analysis, it should not be presented as a result.

## Limitations

- The analysis is observational and does not establish causation.
- Plant dispatch may be endogenous: a plant can turn on or increase output because system conditions are already deteriorating.
- Weather and operating variables can interact, so isolated coefficients should not be interpreted without context.
- The available period may not represent every season or operating regime.
- Engineering validation is required before changing operating controls.

## Tools

**Python · pandas · statsmodels · scikit-learn · SHAP · Power BI · statistical modeling**
