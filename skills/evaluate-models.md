---
name: evaluate-models
description: Evaluate all trained candidates on the same held-out validation data using binary classification metrics and visualisations.
mode: organisational
---

## When to use
Run after training to compare model quality fairly before selection.

## How to execute
1. Load each trained pipeline.
2. Score the same validation split.
3. Calibrate model-specific decision thresholds on validation data.
4. Compute the comparison metrics per model at each model's calibrated threshold: Accuracy, Precision, Recall, F1-score, cost-derived Fβ-score, AUC-ROC.
5. Generate visualizations per model:
- confusion matrix,
- ROC curve,
- threshold sweep curve (score vs threshold with optimal threshold marker).
6. Write metrics and threshold policy back to state.

## Inputs from agent state
- trained_model_paths
- trained_model_families
- split_paths
- model_split_paths

## Outputs to agent state
- eval_metrics
- threshold_calibrations
- evaluation_summary_table
- figures
- threshold_policy
- logs

## Output format
Metrics dictionary, calibrated threshold policy, and in-memory figure objects.

## Notes
This stage must use the same held-out data for every candidate.
Thresholds must not be hardcoded globally.
Use the cost-derived Fβ-score as the default threshold-calibration objective unless the caller explicitly overrides it.
Derive β from the false-negative and false-positive business cost proxies carried by the pipeline state or notebook configuration.
The evaluation summary table used for model selection must reflect calibrated-threshold metrics.
