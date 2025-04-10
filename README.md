# composite-macro-indicator-cmi
A macroeconomic forecasting model inspired by JPMorgan's Composite Macro Indicator (CMI). Uses economic indicators to construct a forward-looking diffusion index that predicts real economic momentum 6 months ahead.


# JPMorgan-Style Composite Macro Indicator (CMI)

This project replicates and customizes a diffusion-index-based approach inspired by JPMorgan's Composite Macro Indicator (CMI). The goal is to construct a forward-looking signal that captures the broad direction of the U.S. economy **6 months ahead** using a selection of macroeconomic indicators.

---

##  Overview

### What is a Diffusion Index?
A diffusion index summarizes changes in multiple indicators into a single metric. In this case, 27 macroeconomic variables (covering growth, inflation, labor, sentiment, etc.) are each scored from **-2 to +2**, based on:

- **Level** relative to historical norms
- **Momentum** (1st derivative)
- **Acceleration** (2nd derivative, filtered by z-scores)

The final index, referred to as **CMI**, ranges from **-100 to +100**, representing net directional strength across the macro landscape.

---

##  Model Objective

The Composite Macro Indicator (CMI) is designed to anticipate the **Coincident Economic Index (CEI)** — a well-established proxy for real-time economic activity — **6 months ahead**.

The CEI includes:
- Non-farm Payroll Employment
- Real Personal Income (ex-transfer payments)
- Industrial Production
- **Real Manufacturing and Trade Sales**

Thus, this model functions as a **macro leading indicator**, not a point forecast of GDP or inflation, but a structured estimate of cyclical direction.

---

##  How the Model is Implemented

### Multiple Validation Paths
The notebook explores more than one way to test the effectiveness of the CMI:

1. **Direct comparison to CEI growth**
   - CMI at time *t* is compared against CEI (monthly % change) at *t + 6 months*
   - This checks how well today’s signal anticipates the direction of the CEI

2. **Forecasting Manufacturing & Trade Sales (MoM %)**
   - An alternative setup where the same CMI is used to explain/predict future changes in Manufacturing & Trade Sales
   - The target variable is still shifted by `-6` to reflect the 6-month forecast horizon:
     ```python
     Y = data['manufactoring and trade sales'].shift(-6)
     ```

These approaches serve as **validation exercises**, not mutually exclusive models. They ensure the CMI reflects future macroeconomic direction regardless of which coincident proxy is used.

---

##  Interpreting the CMI

- **+100** → All indicators strongly expansionary
- **0** → Neutral / mixed signals
- **-100** → All indicators strongly contractionary

We interpret the CMI as a structured economic sentiment index, meant to track broad economic inflections ahead of traditional indicators.

### Validation:
We plot and correlate **CMI(t)** with various economic benchmarks at **t+6**, such as:
- CEI (Coincident Economic Index)
- Real Manufacturing & Trade Sales
- Retail Sales

---

##  Methodology Summary

Each of the 27 indicators is processed using:

- Rolling z-scores (relative to historical mean/std)
- Scoring rules based on:
  - **Level** (absolute value thresholds)
  - **Momentum** (1st derivative)
  - **Acceleration** (2nd derivative with volatility filter)

#### Standard Indicators:
Examples: ISM PMI, Consumer Confidence, Jobless Claims  
These use the same generalized scoring pattern.

#### Custom-Handled Indicators:
Examples: Durable Goods, Oil, Breakeven Inflation  
These have indicator-specific logic based on volatility, ambiguity, or macro context (e.g., oil spikes could mean demand strength or supply disruption).

---

## 🧪 Target Variables & Time Logic

The project supports flexible forecasting:

- `Y = data['CEI'].shift(-6)` → CMI predicts CEI growth 6 months ahead  
- `Y = data['manufactoring and trade sales'].shift(-6)` → CMI predicts future economic sales momentum

**Note:** The input macro indicators are also shifted using `.shift(-6)` to properly align the predictor matrix with the future target variable.

This ensures CMI(t) always aligns with Y(t+6).

---

##  How to Add Your Own Variables

You can easily extend the framework.

### To add new indicators:
1. Add your time series to the input CSV
2. Define a custom scoring function (or reuse existing templates)
   ```python
   def score_my_indicator(series):
       # Custom scoring logic
       return score_series
   ```
3. Append the score output to the aggregated CMI
4. (Optional) Add your variable to the plotting or regression sections

### To change what you’re forecasting:
Update this line:
```python
Y = data['your_target_variable'].shift(-forecast_horizon)
```

---

##  Data Disclaimer

Original Bloomberg data is not included due to licensing constraints.  
Dummy CSV templates are provided to allow testing and expansion with your own inputs.

---

##  Final Notes

This model draws from real institutional practice — combining structured indicator scoring, forecasting alignment, and economic intuition. Whether you're exploring cycles, building policy trackers, or studying macro risk, the framework is flexible and conceptually robust.

