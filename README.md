# 🛒 Walmart Weekly Sales — Predictive Analysis
 
![Language](https://img.shields.io/badge/Language-R-276DC3?style=flat-square&logo=r)
![Models](https://img.shields.io/badge/Models-4%20Regressors-brightgreen?style=flat-square)
![CV](https://img.shields.io/badge/Validation-Time--Based%20Split-orange?style=flat-square)
![Dataset](https://img.shields.io/badge/Dataset-6%2C435%20Rows-blue?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-success?style=flat-square)
 
---
 
## 📌 Overview
 
> Can store location, seasonality, and economic conditions predict Walmart's weekly sales? This project answers that question through exploratory data analysis, statistical testing, and four regression models applied to weekly sales data across 45 Walmart stores from 2010 to 2012.
 
> Variables include store identity, holiday flags, temperature, fuel price, CPI, unemployment, and engineered time features. The target variable is `Weekly_Sales`, log-transformed before modeling to correct for right skew.
 
> The goal is not just prediction accuracy — it's understanding what actually drives retail sales. Which factors matter most? Which common assumptions about economic conditions hold up in the data?
 
---
 
## 🗂️ Dataset
 
- **Source:** [Kaggle — Walmart Store Sales Forecasting](https://www.kaggle.com/datasets/mikhail1681/walmart-sales)
- **Size:** 6,435 rows · 45 stores · Feb 2010 – Oct 2012 · No missing values
- **Target:** `Weekly_Sales` — log-transformed during modeling, exponentiated back to dollars for evaluation
  
| Variable | Type | Description |
|---|---|---|
| `Store` | Factor | Store ID (1–45) |
| `Date` | Date | Week start date |
| `Weekly_Sales` | Numeric | Weekly revenue in USD |
| `Holiday_Flag` | Factor | 1 = holiday week, 0 = non-holiday |
| `Temperature` | Numeric | Average regional temperature (°F) |
| `Fuel_Price` | Numeric | Regional fuel price (USD/gallon) |
| `CPI` | Numeric | Consumer Price Index |
| `Unemployment` | Numeric | Regional unemployment rate (%) |
| `Is_Holiday` | Integer | Engineered binary holiday flag |
| `Is_Q4` | Integer | 1 if October–December |
| `Is_Nov_Dec` | Integer | 1 if November or December |
 
---
 
## 🛠️ Methodology
 
### Pipeline
 
```
Raw Data → Cleaning → EDA → Statistical Tests
    → Feature Engineering / Time-Based Split → 4 Models → Evaluation → Results → Conclusion
```
 
### Preprocessing
- Parsed dates and extracted Year, Month, Week, Quarter as separate features
- Converted `Store` and `Holiday_Flag` to proper factor types
- Full data quality checks — null detection, duplicate rows, zero/negative sales
- Log-transformed `Weekly_Sales` to correct right skew before modeling
- **Time-based train/test split** — trained on 2010–2011, tested on 2012 to prevent data leakage
### Models
 
| Model | Key Setting |
|---|---|
| Linear Regression (Full) | All predictors including 44 Store dummy variables |
| Linear Regression (Stepwise) | AIC backward selection from full model |
| Random Forest | 300 trees, `mtry = 4`, `nodesize = 5` |
| XGBoost | `eta = 0.05`, `max_depth = 5`, early stopping at 30 rounds |
 
### Evaluation
- ✅ LM diagnostic plots (Residuals vs Fitted, Q-Q, Scale-Location, Cook's Distance)
- ✅ VIF multicollinearity check
- ✅ Breusch-Pagan heteroscedasticity test
- ✅ Durbin-Watson autocorrelation test
- ✅ Random Forest feature importance chart
- ✅ XGBoost Actual vs Predicted scatter plot
- ✅ XGBoost Residuals vs Fitted plot
- ✅ Summary metrics table (RMSE, MAE, R²)
---
 
## 📁 Project Structure
 
```
├── Walmart Sales Predictive Analysis.Rmd
├── Walmart.csv
└── README.md
```
 
---
 
## 📊 EDA
 
| Plot | What It Shows | Key Finding |
|---|---|---|
| 1 — Sales Distribution | Histogram + KDE of weekly sales | Most stores fall between **$500K–$1.5M/week**, right-skewed with a few large stores pushing the tail toward **$3.8M** |
| 2 — Sales Over Time | Total weekly sales across all 45 stores with Loess trend | Sales spike every **November–December** and stay relatively **flat** the rest of the year |
| 3 — Holiday Boxplot | Holiday vs Non-Holiday side-by-side | Holiday weeks generally produce slightly **higher sales**. Holiday periods also reduce the likelihood of particularly **poor sales weeks** |
| 4 — Monthly Ridge Plot | Sales distribution stacked by month | **November and December** shift clearly to the right while **January** drops back down as the weakest month |
| 5 — Top 10 Stores | Average weekly sales ranked by store | **Store 20 leads at $2.1M/week** nearly double the overall average, with a wide gap between the top stores and the rest |
| 6 — Scatter Plots | Temperature, Fuel Price, Unemployment, CPI each vs sales | **Unemployment and CPI** show a slight downward slope. **Fuel Price** is completely flat with no relationship to sales |
| 7 — Correlation Heatmap | Pearson correlations across all numeric features | **Unemployment** has the strongest negative correlation with sales (r = -0.106) everything else is close to zero, especially Fuel Price (r = 0.009) |
 
---
 
## 🧪 Statistical Analysis
 
| Test | Result | Finding |
|---|---|---|
| Descriptive Stats | Mean ($1.05M) > Median ($961K) | Right skew confirmed — a handful of high-volume stores pull the average up |
| Welch T-Test | t = -2.68, p = 0.0076 | Holiday weeks average **$1.12M vs $1.04M** — the difference is statistically real |
| One-Way ANOVA | F = 13.57, p < 0.001 | At least one quarter has significantly different sales. **Q4 drives this result** |
| Pearson Correlations | Unemployment r = -0.106 ✅, Fuel Price r = 0.009 ❌ | Economic variables are weak predictors. **Fuel Price has no significant relationship** with sales |
| STL Decomposition | Clear annual peaks, slight upward trend (Store 1) | Seasonality is real but the large remainder confirms **non-seasonal factors explain most variation** |
 
---
 
## 🔬 Diagnostic Tests
 
Run on the linear regression model to check whether core assumptions held up.
 
| Test | Result | What It Means |
|---|---|---|
| VIF Check | Several high values | Expected due to overlapping time features and 44 store dummies — kept since goal is prediction not interpretation |
| Breusch-Pagan | p < 2.2e-16 | Heteroscedasticity confirmed — bigger stores have larger errors |
| Durbin-Watson | DW = 1.44, p < 2.2e-16 | Autocorrelation present — nearby weeks influence each other |
| Residuals vs Fitted | Mostly random scatter | Model fits well overall with a few outliers |
| Q-Q Plot | Tails deviate from normal | Residuals aren't perfectly normal, driven by high-volume stores |
| Scale-Location | Spread mostly consistent | Variance assumption reasonably met |
| Cook's Distance | No point dominates | Outliers exist but none distort the model |
 
---
 
## 🏆 Model Results
 
All models trained on 2010–2011 and evaluated on a held-out 2012 test set (~1,935 rows).
 
| Model | RMSE (USD) | MAE (USD) | R² |
|---|---|---|---|
| **Linear Regression** ⭐ | **$107,837** | **$76,142** | **0.9596** |
| **Stepwise LM** ⭐ | **$107,837** | **$76,142** | **0.9596** |
| XGBoost | $155,633 | $103,786 | 0.9159 |
| Random Forest | $163,287 | $108,039 | 0.9074 |
 
> ⭐ **Linear Regression and Stepwise LM** tied for best. Stepwise retained all features — confirming the original feature set was already well chosen. All four models exceeded **R² = 0.90**.
 
**Why did Linear Regression beat the tree models?**
 
When `Store` is passed in as a categorical variable with 45 levels, R automatically creates **44 store dummy variables** behind the scenes — giving the model a separate baseline for each store's typical sales. That per-store starting point is hard for tree models to replicate cleanly, since Random Forest and XGBoost have to figure out store-level patterns through splits rather than having them handed over directly.
 
**Metrics explained:**
- **RMSE** — average prediction error in dollars, penalises large mistakes more heavily
- **MAE** — average dollar gap between predicted and actual (easier to explain)
- **R²** — proportion of sales variance explained (0 = nothing, 1 = perfect)
---
 
## 🔍 Key Findings
 
- 🏪 **Store identity** is the single strongest predictor — dominates the RF feature importance chart by a massive margin
- 📅 **Week of the year** is the second most important feature — confirming seasonality is the other major driver
- 🎄 **Holiday weeks** produce a real but modest boost — statistically significant (p = 0.0076)
- 📉 **Unemployment and CPI** rank surprisingly high in RF feature importance despite weak individual correlations
- ⛽ **Fuel Price** is the weakest variable across every analysis — no significant relationship with sales (r = 0.009, p = 0.45)
- 📈 **XGBoost** outperformed Random Forest (R² 0.9159 vs 0.9074) by correcting errors step by step rather than averaging independent trees
---
 
## 📌 Project Summary
 
This project predicts weekly sales across 45 Walmart stores using four regression models — linear regression, stepwise regression, random forest, and XGBoost — applied to data from February 2010 to October 2012. Models were trained on 2010–2011 and tested on 2012 to prevent data leakage, with the linear model coming out on top at R² = 0.9596.
 
The main finding is straightforward: **which store it is and what time of year it is explain most of the variation in sales.** Economic variables like fuel price turned out to have almost no relationship with sales at all. All four models exceeded R² = 0.90, showing that weekly retail sales are highly predictable once you account for store identity and seasonality.
 
---
 
## ⚠️ Limitations
 
- The model was trained on 2010–2012, a fairly stable economic stretch. It hasn't seen a major recession, supply disruption, or a shift in how people shop — so predictions outside that window should be treated with caution.
- The Breusch-Pagan test confirmed heteroscedasticity (p < 2.2e-16) — meaning the model's prediction errors get larger as store volume goes up. It's most reliable in the middle of the sales range, less so at the high end.
- The Durbin-Watson test (DW = 1.44, p < 2.2e-16) flagged autocorrelation — sales in one week are influenced by the week before. A time-series model like ARIMA or Prophet would handle this more directly.
- Each store gets its own coefficient, so add a new store and the model has no reference point for it. It learned *who* each store is, not *why* stores perform differently.
- There's no data on store size, local demographics, nearby competitors, or promotions — all of which likely explain a good chunk of the variation between stores.
- Several variables overlap (time features especially), so VIF values came back high. The model is built for prediction — reading too much into individual coefficients would be misleading.
---
 
## ▶️ How to Run
 
> 🌐 **View the full published report on RPubs:** [Walmart Weekly Sales — Predictive Analysis](https://rpubs.com/Priesthood162002/1439381)
 
1. Clone or download this repository
2. Place `Walmart.csv` in the same folder as the `.Rmd` file
3. Open `Walmart Sales Predictive Analysis.Rmd` in RStudio
4. Install required packages and knit:
```r
install.packages(c(
  "tidyverse", "lubridate", "scales", "corrplot", "ggridges",
  "patchwork", "caret", "randomForest", "xgboost",
  "Metrics", "car", "lmtest", "knitr"
))
```
 
---
 
## 📦 Dependencies
 
| Package | Purpose |
|---|---|
| `tidyverse` | Data wrangling + ggplot2 |
| `lubridate` | Date parsing |
| `scales` | Axis formatting |
| `corrplot` | Correlation heatmap |
| `ggridges` | Monthly ridge plot |
| `patchwork` | Multi-panel plot layout |
| `randomForest` | Random Forest model |
| `xgboost` | Gradient boosting model |
| `car` / `lmtest` | VIF, Breusch-Pagan, Durbin-Watson |
| `knitr` | Table formatting in RMarkdown |
 
---
 
## 🧰 Skills Demonstrated
 
`R` · `RMarkdown` · `ggplot2` · `Regression Modeling` · `Stepwise Selection` · `Random Forest` · `XGBoost` · `Time-Series Decomposition` · `Statistical Hypothesis Testing` · `EDA` · `Feature Engineering` · `Data Leakage Prevention` · `Panel Data Modeling` · `Reproducible Research`
 
---
 
## 🙋 Author
 
Made by **Royalpriesthood Olola** · [GitHub](https://github.com/Royalpriesthood3260) · [LinkedIn](https://www.linkedin.com/in/royalpriesthoodolola)
