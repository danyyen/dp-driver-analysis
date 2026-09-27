# District Cooling Service-Risk Analytics

### Finding when customer pressure is most at risk—and what operations should watch before service fails

**Python · pandas · statsmodels · scikit-learn · SHAP · Power BI**

A customer pressure failure is the end of the story. Operations needs earlier signals.

This analysis combines 5-minute plant telemetry with weather conditions to investigate **when customer differential pressure (DP) deteriorates, which operating conditions move with that deterioration, and what should be monitored before the 12 PSI service target is crossed**.

> **Business question:** When does DP risk increase, what conditions accompany it, and where can operations focus attention before service falls below target?

## Executive finding

The clearest operational signal is **time concentration**.

In the validated comparison, **37.9% of observations during the 13:00–17:00 window were below 12 PSI, versus 15.4% outside that window**.

That is roughly a **2.5× higher observed breach rate** during the afternoon window.

The practical implication is not “change one control because a model coefficient is large.” It is to treat the afternoon period as a higher-risk operating regime and monitor customer DP, system flow, weather load, output pressure, and plant dispatch together.

## Analysis workflow

```text
5-minute plant telemetry + hourly weather
                  ↓
       Data quality + alignment
                  ↓
         Peak-risk comparison
                  ↓
   Multivariable regression (HAC)
                  ↓
      Interaction / SHAP analysis
                  ↓
        Operational monitoring
```

The analysis uses multiple methods because each answers a different question:

- **descriptive analysis** identifies when risk concentrates;
- **regression** separates overlapping relationships while controlling for other measured conditions;
- **interaction analysis** tests whether relationships change across operating regimes; and
- **SHAP** provides an additional model-interpretation view rather than a causal ranking.

## What the data says

### 1. Afternoon operation deserves disproportionate attention

The 13:00–17:00 window shows a substantially higher observed rate of DP falling below the 12 PSI target: **37.9% vs. 15.4%** outside that period.

For an operator, that converts a broad “watch pressure” instruction into a more specific monitoring question: **what is changing as the system enters the afternoon demand window?**

### 2. Weather and flow provide demand context

Humidex and system flow move negatively with DP in the observed data. They are useful context for identifying periods when the network is under greater demand pressure.

They should not be interpreted as isolated causes. Weather, flow, dispatch, and pressure are part of the same operating system and can move together.

### 3. Output pressure has a positive adjusted relationship with customer DP

After controlling for the other modeled variables, output pressure retains a positive relationship with customer DP.

That makes it operationally relevant, but the coefficient is **not** treated as a direct setpoint recommendation. Control changes require engineering validation because observational data cannot establish the physical response to an intervention.

### 4. Plant coefficients require operational context

Plant 1 and Plant 2 coefficients are not interpreted as simple “good plant / bad plant” effects.

Dispatch can be endogenous: a plant may start or increase output **because** load is already rising or DP is already deteriorating. In that situation, the model can capture both plant behaviour and the operating condition that triggered it.

That is why the project separates **diagnostic evidence** from **causal claims**.

## A modeling trap I deliberately avoided

Raw regression coefficients are not a valid importance ranking when variables use different units and scales.

A coefficient measured per PSI cannot be compared directly with one measured per ton, GPM, or degree of humidex and then declared the “strongest driver.”

Instead, the model is used to understand adjusted relationships and operating context. Any importance comparison requires a common scale or an appropriate interpretation method.

This distinction matters because a statistically impressive result can still lead to a poor operational recommendation if its units, endogeneity, or physical context are ignored.

## From analysis to an operating response

The evidence supports a simple control-room workflow:

1. **Track customer DP against 12 PSI continuously.**
2. **Escalate attention before and during 13:00–17:00.**
3. **Read pressure together with flow, humidex and plant dispatch—not in isolation.**
4. **Investigate deterioration while margin still exists rather than after a threshold breach.**
5. **Use engineering review or controlled operating tests before changing setpoints based on model output.**

A production implementation could turn these signals into a risk-monitoring layer that warns operators when several adverse conditions begin converging.

## What this project demonstrates

The technical work is only part of the project. The larger skill is translating noisy operational telemetry into a recommendation that respects both statistics and engineering reality.

The project demonstrates:

- time-series operational analysis;
- joining telemetry with external weather data;
- regression with heteroskedasticity/autocorrelation-consistent inference;
- interaction analysis and model interpretation;
- distinguishing association from causation;
- recognizing dispatch endogeneity;
- translating analytical evidence into monitoring decisions; and
- communicating limitations before recommending operational change.

## Repository structure

```text
notebooks/       reproducible analysis notebooks
src/             reusable analysis code
data/            sanitized public analysis inputs
result_images/   validated analytical visuals
docs/            supporting documentation
```

## Reproducibility standard

The public project uses neutral field names and repository-relative paths. It should not depend on local machine paths, employer/customer identifiers, private case materials, or inaccessible source files.

Every headline metric presented here should be reproducible from the public analysis. If a result cannot be reproduced, it does not belong in the README.

## Limitations

- This is observational analysis; it does **not** establish causation.
- Plant dispatch may respond to the same conditions affecting DP, creating endogeneity.
- Weather and operating variables interact and should not be interpreted as independent physical levers.
- The available observation period may not represent every season or operating regime.
- Engineering validation is required before changing operating controls or setpoints.

---

**What I would discuss in an interview:** how I moved from a threshold-breach problem to a risk-window analysis, why I refused to rank raw coefficients across incompatible units, how dispatch endogeneity changes the Plant 1/Plant 2 interpretation, and how I would turn the analysis into a monitored operational decision system.
