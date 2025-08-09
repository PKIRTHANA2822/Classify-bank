# Project 
- classif-bank-dataset-graphviz-gain-lift-chart
-   <img width="240" height="240" alt="dataset-thumbnail" src="https://github.com/user-attachments/assets/621ad17c-07a5-415d-92f9-c8175a747429" />
# Objective
- Predict customers who are likely to cancel (churn) their Colab paid product contracts within the next 30 days and provide a prioritized list that marketing/support can act on to maximize retained revenue.
### Measurable goals:
- Lift@10% ≥ 3 (three times better than random) OR Gain@10% ≥ X% (decided with business).
- Improvement in net retained revenue ≥ business cost threshold (example ROI > 2x for retention campaign).
- Properly calibrated probabilities (Brier score target set in baseline experiments).
# Why we use this project
### Reduce revenue leakage: Retaining even a small percentage of high‑LTV customers saves more than acquiring new ones.
### Use limited resources effectively: Marketing/support budgets are finite — this ranks customers so you contact the most valuable at‑risk ones first.
### Data‑driven decision making: Convert model output into clear operational actions (contact lists, offer types, channels).
### Measureable impact: Gain/Lift charts translate model performance to expected campaign yields (easily communicated to stakeholders).
# Step‑by‑step approach (practical)
- Define business problem & metrics
- Churn window (30 days), contact budget, cost per contact, avg LTV — agree on targets.
- Collect & join data
- Pull customer, billing, usage, support, and campaign logs. Timestamp everything and join on customer_id + snapshot date.
- Create labeled snapshots
- For each snapshot date generate features using only prior data and label whether churn occurred in the next 30 days.
-EDA & quality checks (see EDA section)
- Feature engineering & selection (see FE & selection)
# Training & validation
- Use time‑aware train/validation/test splits.
- Try multiple models and tune hyperparameters using cross‑validation.
- Calibration & gain/lift evaluation
- Calibrate probabilities.
- Build Gain/Lift and per‑decile ROI calculators.
# Explainability
- Generate SHAP explanations for global and local insights.
- Deployment & experiment
- Batch scoring + export top‑N. Run A/B tests on retention offers and measure incremental retention.
# Monitoring & retraining
- Monitor lift, calibration, and feature drift. Retrain when performance drops.
- Exploratory Data Analysis (what to run and why)
# Quick checklist & recommended plots:
- Baseline churn rate and trend by week/month (time series).
- Churn rate by categorical groups (plan_type, segment, region, payment_method).
- Histograms / KDEs for numeric features split by churn label (tenure, monthly_charges, usage metrics).
- Boxplots to inspect outliers (billing, usage).
- Correlation heatmap and pairwise scatter for top numeric features (check collinearity).
- Missingness heatmap and imputation plan.
- Cohort analysis (churn by signup cohort) to detect temporal shifts.
# Feature selection (practical recipe)
- Remove leakage: drop features that would not be available at prediction time (e.g., cancel_reason recorded after cancel).
- Filter low utility features: very high missingness (>50%) or near‑zero variance.
- Univariate ranking: mutual information for numeric, chi² for categoricals — shortlist top K.
- Embedded ranking: train a tree model and use feature importances.
- Stability & production feasibility: prefer features stable over time and easy to compute weekly.
- Permutation importance: confirm performance drop when removing candidate features.
- Aim for a compact, explainable feature set (20–50 features) balancing predictive power and engineering cost.
# Feature engineering — concrete ideas
- Time-windowed aggregates (most powerful):
- usage_7d_sum, usage_30d_mean, usage_90d_trend (slope)
- tickets_7d_count, tickets_30d_type_flag
- payments_missed_30d, days_since_last_payment
# Behavioral flags:
- recent_login_7d (boolean)
- downgrade_last_30d, removed_feature_flag
- Demographics & billing transforms:
- tenure_months, avg_monthly_charge, charge_per_active_day
- balance_bin (quantiles) or log_balance
# Interaction terms:
- plan_type × overdue_count, usage_trend × tickets_30d
# Binning & encodings:
- Bucket tenure and charges into deciles for tree‑agnostic models.
- Use Target/Mean encoding for high-cardinality categories (with smoothing & CV to avoid leakage).
- Feature validation: always calculate features only from pre‑snapshot data and keep reproducibility in pipeline code.
# Model training (recommended setup)
- Preprocessing pipeline: ColumnTransformer with imputation, scaling for numerics and OneHot/Target encoding for categoricals.
# Models to try (in this order):
- Logistic Regression (calibrated) — interpretable baseline.
- Random Forest / XGBoost / LightGBM — strong performance, handle nonlinearity.
- CatBoost — if many categorical features.
# Class imbalance strategies:
- class_weight='balanced' for LR and RF.
- SMOTE/SMOTENC or upsampling for tree models if needed (use pipeline integration).
- Evaluate with business metrics (lift/gain) — not just accuracy.
- Hyperparameter tuning: RandomizedSearchCV or Optuna with time‑aware CV. Track: n_estimators, max_depth, learning_rate, subsample, colsample_bytree, min_child_weight.
- Probability calibration: Use CalibratedClassifierCV (sigmoid/isotonic) for Platt scaling if probabilities are miscalibrated.
# Model testing & evaluation
### Primary metrics:
- Gain/Lift (primary business metric): compute deciles and evaluate Gain@10%, Lift@10%.
- ROC‑AUC and PR‑AUC (use PR‑AUC for imbalanced data).
- Brier score and calibration plots.
- Confusion matrix at chosen operational threshold(s): precision/recall/F1.
# Gain/Lift recommended steps:
- Sort by predicted probability descending.
- Split into deciles (or custom buckets).
- Compute cumulative churn captured and convert to Gain% and Lift.
# Operational testing:
- Backtest: simulate contacting top‑N in historical data and compute expected retained customers and ROI.
- A/B test plan: randomize offers in production to measure incremental retention vs control.
# Robustness checks:
- Temporal validation (train on past, test on future periods).
- Feature drift & label drift checks.
- Fairness and bias checks if applicable.
Output / Deliverables
<img width="665" height="275" alt="Screenshot 2025-08-08 194216" src="https://github.com/user-attachments/assets/bcc10fcf-7ce4-4003-9f95-623c8d9a9baf" />

