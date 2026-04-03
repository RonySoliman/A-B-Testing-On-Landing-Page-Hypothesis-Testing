# 🤖 A/B Test Results Analysis — Statistical Testing & Logistic Regression

A comprehensive statistical analysis project that evaluates whether a new webpage design leads to higher user conversion rates using **descriptive statistics**, **probability**, **hypothesis testing via bootstrapped simulation**, and **logistic regression** — building from first principles up to a full model.

Built as part of the **Udacity Data Analyst Nanodegree** — Statistics & Probability module.

---

## 📌 Project Overview

An e-commerce company ran an A/B test exposing users to either a **control page** (old design) or a **treatment page** (new design). The goal is to determine whether the new page produces a statistically significant improvement in conversion rates, using three independent approaches and arriving at a consistent conclusion across all three.

---

## 📁 Project Structure

```
├── Analyze_ab_test_results_notebook_new_my_version.ipynb   # Main analysis notebook
├── ab_data.csv                                              # A/B test dataset
└── README.md
```

---

## 🗂️ Dataset

**File:** `ab_data.csv`

| Column | Description |
|---|---|
| `user_id` | Unique user identifier |
| `timestamp` | Time of visit |
| `group` | `control` or `treatment` |
| `landing_page` | `old_page` or `new_page` |
| `converted` | 1 = converted, 0 = did not convert |
| `country` | Country of the user: `US`, `UK`, `CA` |

---

## 🔬 Analysis Structure

---

### Part I — Descriptive Statistics

- Loaded and inspected `ab_data.csv`
- Confirmed **no missing values** across all columns
- Identified `converted` as the only non-categorical (numeric) column
- Computed group sizes: treatment vs. control visitor counts
- Visualised visitor counts by country (bar chart)
- Confirmed `converted` takes only binary values (0 or 1)

**Overall conversion rate: ~12%**

---

### Part II — Probability

Computed conditional and marginal conversion probabilities:

| Probability | Value |
|---|---|
| P(converted) — overall | ~12% |
| P(converted \| control group) | ~10.7% |
| P(converted \| treatment group) | ~15.8% |
| P(individual in treatment) | ~50% |
| P(individual from Canada) | computed |
| P(converted \| US) | ~10.7% |
| P(converted \| UK) | ~10.2% |

**Conversion by country and group (full table):**

|           | US    | UK     | CA    |
|---|---|---|---|
| Control   | 10.7% | 10.165% | 9.45% |
| Treatment | 15.8% | 14.87%  | 15.40% |

**Key finding:** Treatment group consistently outperforms control across all three countries, suggesting the page effect is not country-specific.

---

### Part III — Hypothesis Testing (Bootstrapped Simulation)

**Hypotheses:**

$$H_0: p_{treatment} - p_{control} \leq 0$$
$$H_1: p_{treatment} - p_{control} > 0$$

**Approach (Type I error rate: α = 0.05):**

1. Computed the null conversion rate (pooled rate across both groups)
2. Simulated 500 iterations of treatment/control conversion differences under the null using `np.random.choice`
3. Computed the observed difference between treatment and control in the real data
4. Calculated the **p-value** as the proportion of simulated differences exceeding the observed difference
5. Plotted a histogram of the 500 simulated `p_diffs`

**Result:**
> **p-value ≈ 0** → Reject the null hypothesis. There is evidence that the treatment page produces a higher conversion rate than the control page.

---

### Part IV — Logistic Regression

Validated the hypothesis test result using two logistic regression models via `statsmodels`.

**Model 1 — Page effect only:**
- Response: `converted`
- Predictor: `ab_page` (1 = treatment, 0 = control)
- Created intercept column and dummy variable for group

**Model 2 — Page + Country interaction:**
- Response: `converted`
- Predictors: `ab_page`, `CA`, `UK` (US as reference category via dummy encoding)
- Tested whether country membership modifies the conversion effect

**Conclusions from regression:**

| Model | Finding |
|---|---|
| Model 1 (page only) | `ab_page` p-value consistent with hypothesis test — treatment page effect is significant |
| Model 2 (page + country) | `CA` and `UK` p-values > 0.05 → **fail to reject** null that country impacts conversion; country is not a significant moderator |

---

## 📊 Visualisations

| Plot | Purpose |
|---|---|
| Bar chart — visits by country | Understand geographic distribution of the sample |
| Histogram of `p_diffs` (500 simulations) | Visualise the null distribution of conversion rate differences |

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| `pandas` | Data loading, filtering, groupby aggregations |
| `numpy` | Binomial simulation, array operations |
| `matplotlib` | Histogram and bar chart visualisation |
| `statsmodels` | Logistic regression (Logit model) |
| `random` | Seed setting for reproducibility |

---

## 🚀 How to Run

1. Place `ab_data.csv` in the same directory as the notebook
2. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib statsmodels
   ```
3. Launch Jupyter:
   ```bash
   jupyter notebook Analyze_ab_test_results_notebook_new_my_version.ipynb
   ```
4. Run all cells in order — `random.seed(0)` is set in Cell 1 for reproducibility

---

## ✅ Final Conclusions

All three analytical approaches converge on the same answer:

1. **Probability analysis** — Treatment group converts at ~15.8% vs control at ~10.7%
2. **Bootstrap simulation** — p-value ≈ 0, reject H₀ at α = 0.05
3. **Logistic regression** — `ab_page` coefficient confirms significant treatment effect; country variables are not significant

> The new page (treatment) produces a statistically significantly higher conversion rate. Country of origin does not meaningfully interact with the page effect.

---

## 📝 Notes & Limitations

- The simulation uses 500 bootstrap iterations — increasing to 10,000+ would give a more stable p-value estimate
- Logistic regression assumes independence of observations; repeated visits by the same user could violate this
- The analysis does not account for temporal effects (novelty bias) — users may convert more simply because the page is new
- No correction for multiple comparisons was applied when testing across countries

---

## 👤 Author

Built as a submission for the **Udacity Data Analyst Nanodegree** — Practical Statistics & A/B Testing module.
