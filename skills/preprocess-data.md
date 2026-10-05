---
name: preprocess-data
description: Load claims data, run deterministic feature engineering, and create reproducible train/validation/test splits.
mode: organisational
---

## When to use
Use this skill at the start of the pipeline whenever raw claims data is provided or the preprocessing logic changes.

## How to execute
1. Load the raw CSV.
2. Map target label `fraud_reported` from {Y,N} to {1,0}.
3. Engineer date-derived feature `days_since_policy_bind_to_incident` and drop raw date columns.
4. Identify categorical variables as ordinal vs nominal before training begins.
5. Create stratified train/validation/test splits.
6. Materialize model-ready datasets for each model family:
- logistic regression: ordinal integer encoding; nominal one-hot encoding with `drop='first'`
- catboost: ordinal integer encoding; nominal columns kept as raw categorical strings
- lightgbm: ordinal integer encoding; nominal columns kept as categorical dtype
7. Save raw splits, model-ready splits, and any fitted preprocessors to disk and write pointers to agent state.

## Inputs from agent state
- raw_data_path
- artifact_dir

## Outputs to agent state
- dataset_profile
- target_column
- split_paths
- model_split_paths
- model_preprocessor_paths
- feature_columns
- categorical_columns
- numeric_columns
- nominal_categorical_columns
- ordinal_categorical_columns
- ordinal_category_orders
- nominal_category_values
- logs

## Output format
State keys updated with dataset profile, categorical role metadata, raw split paths, model-ready split paths, fitted preprocessor paths, and audit log.

## Notes
This skill is deterministic Python, not an LLM free-form stage.
