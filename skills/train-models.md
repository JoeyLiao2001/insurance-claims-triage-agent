---
name: train-models
description: Train three candidate models on the same processed claims dataset.
mode: organisational
---

## When to use
Run after preprocessing to fit all candidate models for comparison.

## How to execute
1. Load the model-ready training datasets produced by preprocessing.
2. Select the correct prepared dataset for each candidate model family.
3. Train each candidate on the same training split.
4. Save trained model artifacts.

## Candidate models and configurations
1. `logreg_baseline`
- Preprocessing input expected from preprocessing stage: ordinal categorical variables already integer encoded; nominal categorical variables already one-hot encoded with `drop='first'`; numeric variables already scaled and imputed as required.
- Model: `LogisticRegression(max_iter=2000, class_weight='balanced', random_state=42)`.

2. `catboost`
- Preprocessing:
  - preprocessing stage should already provide ordinal categorical variables as integers
  - preprocessing stage should already preserve nominal categorical variables as raw categorical columns
  - training only needs to pass nominal column names through `cat_features`
- Model: `CatBoostClassifier(iterations=250, depth=6, learning_rate=0.05, loss_function='Logloss', eval_metric='AUC', auto_class_weights='Balanced', random_seed=42, verbose=0)`.

3. `lightgbm`
- Preprocessing:
  - preprocessing stage should already provide ordinal categorical variables as integers
  - preprocessing stage should already preserve nominal categorical variables as pandas categorical dtype
  - training only needs to pass nominal column names through as `categorical_feature`
- Model: `LGBMClassifier(n_estimators=250, learning_rate=0.05, max_depth=-1, num_leaves=31, subsample=0.9, colsample_bytree=0.9, is_unbalance=True, random_state=42, verbosity=-1)`.

## Inputs from agent state
- split_paths
- model_split_paths
- nominal_categorical_columns
- artifact_dir

## Outputs to agent state
- trained_model_paths
- trained_model_families
- logs

## Output format
State keys updated with saved model paths and audit log.

## Notes
This stage should not build encoders or transform raw categorical columns. It should only consume the prepared datasets emitted by preprocessing and fit the model objects.
The training node reads `y_train` from `split_paths` and saves fitted artifacts under `artifact_dir`.
Class imbalance is handled through a combination of stratified data splits, `class_weight='balanced'` for logistic regression, `auto_class_weights='Balanced'` for CatBoost, and `is_unbalance=True` for LightGBM.
