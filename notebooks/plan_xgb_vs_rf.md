# Implementation Plan: XGBoost vs Random Forest Comparison Notebook

**File:** `notebooks/xgboost_vs_random_forest.ipynb`  
**Audience:** Practitioners with working ML knowledge; focused comparison  
**Library:** `xgboost` (XGBClassifier) vs `sklearn` RandomForestClassifier  
**Datasets:** `make_moons` (nonlinear, noisy) + `make_classification` (linear, separable)

---

## Notebook Structure

### Section 0 — Imports & Setup
- Standard imports: numpy, matplotlib, sklearn, xgboost
- Shared constants: random seed, figure sizes, colour palette
- Helper: `make_mesh_grid()` for decision boundary plotting

---

### Section 1 — The Conceptual Why
Markdown-heavy section explaining the fundamental architectural difference:

| | Random Forest | XGBoost |
|---|---|---|
| Strategy | Bagging (parallel, independent trees) | Boosting (sequential, corrective trees) |
| Bias–Variance | Low variance, moderate bias | Low bias, moderate variance |
| Regularisation | Implicit via bootstrap sampling + feature subsampling | Explicit: L1/L2 penalties, `subsample`, `colsample_bytree` |
| Overfitting risk | Lower by default | Higher without tuning |
| Strength | Robust out-of-the-box, noisy data | High accuracy on structured/tabular data |

---

### Section 2 — Dataset Generation
- `make_moons(n_samples=500, noise=0.25)` — two interleaved crescents, nonlinear boundary
- `make_classification(n_samples=500, n_features=2, n_informative=2, n_redundant=0)` — linear-ish, clean separation
- Plot both datasets side-by-side to orient the reader

---

### Section 3 — Decision Boundary Comparison
For **each dataset**:
- Fit both models with default/reasonable hyperparameters
- Plot 2×2 grid: RF vs XGBoost × Moons vs Linear
- Include training accuracy in the title

**Why it's interesting:** XGBoost's additive structure carves sharper, more jagged boundaries;  RF produces smoother boundaries due to averaging. On the moons dataset this is visually stark.

---

### Section 4 — Overfitting & Bias–Variance Demo
Using the **Moons** dataset (noisier, more interesting):

1. **Depth sweep:** fit both models at `max_depth ∈ {2, 6, None}`, plot decision boundaries in a 2×3 grid
2. **Narrative:** explain *why* deep XGBoost memorises noise (each tree corrects residuals with no capacity limit) vs RF (deep individual trees are averaged out by the ensemble)
3. Show train vs test accuracy table per depth setting

---

### Section 5 — Hyperparameter Exploration
Focus on the 2–3 most impactful parameters for each model:

**Random Forest**
| Parameter | Values | What it controls |
|---|---|---|
| `n_estimators` | 10, 100, 500 | Ensemble size; diminishing returns past ~100 |
| `max_depth` | 5, 15, None | Individual tree capacity |
| `max_features` | "sqrt", "log2", 1.0 | Feature subsampling per split (diversity lever) |

**XGBoost**
| Parameter | Values | What it controls |
|---|---|---|
| `learning_rate` | 0.01, 0.1, 0.3 | Step size for each corrective tree |
| `max_depth` | 3, 6, 10 | Tree capacity per iteration |
| `subsample` | 0.5, 0.75, 1.0 | Row subsampling (stochastic boosting) |

For each sweep: plot test accuracy as a bar chart or line plot across values, **on both datasets**.  
Narrative: explain interactions (e.g. low `learning_rate` requires high `n_estimators`).

---

### Section 6 — Learning Curves
Using `sklearn.model_selection.learning_curve`:
- Training set sizes from 5% → 100%
- Plot mean ± std for both train and validation score
- 2×2 grid: RF vs XGBoost × Moons vs Linear
- **Why:** reveals how each model scales with data; XGBoost often needs more data to avoid overfitting; RF plateaus earlier but more stably

---

### Section 7 — ROC Curves
- `sklearn.metrics.RocCurveDisplay`
- Both models, both datasets — one figure with 2 subplots
- Include AUC scores in the legend
- **Why:** calibration and discrimination are the real test; decision boundaries are illustrative but AUC is the headline metric

---

### Section 8 — Summary
Markdown table: head-to-head on every metric across both datasets (accuracy, AUC, fit time).  
Closing narrative: rules of thumb for choosing one over the other.

---

## Dependencies
- `xgboost>=3.2.0` ✅ (just installed)
- `scikit-learn`, `numpy`, `matplotlib` ✅

## Estimated Sections
8 sections, ~25–30 cells total (mix of markdown + code)
