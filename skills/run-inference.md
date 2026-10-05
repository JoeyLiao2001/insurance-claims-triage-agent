---
name: run-inference
description: Run the selected model on a new claim input and produce a structured triage signal for downstream business skills.
mode: organisational
---

## When to use
Run after model selection whenever a user submits a new claim.

## How to execute
1. Load the selected saved pipeline.
2. Transform the raw claim input using the same feature-engineering logic used upstream in preprocessing.
3. Score the prepared claim.
4. Apply the selected model's calibrated decision threshold.
5. Compute decision confidence relative to that calibrated threshold.
6. Assign both a review recommendation and a risk tier, then write the raw inference result to state.

## Inputs from agent state
- inference_input
- selected_model_path
- selected_model_family
- model_preprocessor_paths
- nominal_categorical_columns
- ordinal_category_orders
- nominal_category_values
- threshold_policy
- selected_model_id

## Outputs to agent state
- inference_result_raw

## Output format
Structured dict with risk score, predicted label, review recommendation, threshold, risk tier, risk_tier_thresholds, decision margin, model confidence, and confidence band.

## Notes
This output is not shown directly to business users.
Use the selected model's calibrated threshold from `threshold_policy.decision_thresholds`.
If the selected model needs a fitted preprocessor, inference should reuse the one persisted by preprocessing instead of rebuilding encoders inside the training stage.
Do not assume UI inputs are already model-ready features; raw claim inputs must be normalized first.
`model_confidence` should reflect how far the final decision is from the calibrated threshold, not just the raw fraud probability.
