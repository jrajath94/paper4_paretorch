# ParetOrch Experiment Data Audit

**Files audited:**
- `paper/experiments/pareto_results.json`
- `paper/experiments/pareto_tasks.json`

**Date:** 2026-03-17
**Auditor:** Automated consistency checker

---

## 1. Task Count and Difficulty Tier Breakdown

**Verdict: PASS**

Total tasks in `pareto_tasks.json`: **84** (confirmed by enumeration)

| Tier    | Task IDs                                      | Count |
|---------|-----------------------------------------------|-------|
| Trivial | T001-T012, T061-T064, T080                    | 17    |
| Easy    | T013-T025, T065-T068, T079, T081              | 19    |
| Medium  | T026-T042, T069-T073, T082-T083               | 24    |
| Hard    | T043-T060, T074-T078, T084                    | 24    |
| **Total** |                                             | **84** |

Task IDs T001--T084 are contiguous with no gaps or duplicates.

---

## 2. Topology Cost Multipliers

**Verdict: PASS (with minor rounding note)**

Expected: `hierarchical_total / flat_total = 2.1`, `debate_total / flat_total = 2.8`

| Tier    | Flat Total | Hier Total | Debate Total | Hier/Flat | Debate/Flat |
|---------|-----------|------------|--------------|-----------|-------------|
| Trivial | 600       | 1260       | 1680         | 2.1000    | 2.8000      |
| Easy    | 1340      | 2815       | 3752         | 2.1007    | 2.8000      |
| Medium  | 2950      | 6195       | 8260         | 2.1000    | 2.8000      |
| Hard    | 5600      | 11760      | 15680        | 2.1000    | 2.8000      |

**Minor note:** Easy-tier hierarchical ratio is 2.1007 instead of exactly 2.1, due to input token rounding (890 * 2.1 = 1869 but reported as 1870, a 1-token difference). The stated totals (input + output) are self-consistent within each row.

---

## 3. Cost Per Task USD

**Verdict: PASS (with one truncation note)**

Formula: `(input_tokens * 0.0025/1000) + (output_tokens * 0.01/1000)`

| Topology     | Tier    | Computed  | Reported | Match? |
|-------------|---------|-----------|----------|--------|
| Flat        | Trivial | 0.00285   | 0.0029   | Yes (rounding) |
| Flat        | Easy    | 0.006725  | 0.0067   | Yes (rounding) |
| Flat        | Medium  | 0.015625  | 0.0156   | Yes (rounding) |
| Flat        | Hard    | 0.0320    | 0.0320   | Exact  |
| Hierarchical| Trivial | 0.0060    | 0.0060   | Exact  |
| Hierarchical| Easy    | 0.014125  | 0.0141   | Yes (rounding) |
| Hierarchical| Medium  | 0.032813  | 0.0328   | Yes (rounding) |
| Hierarchical| Hard    | 0.0672    | 0.0672   | Exact  |
| Debate      | Trivial | 0.00798   | 0.0080   | Yes (rounding) |
| Debate      | Easy    | 0.01883   | 0.0188   | Yes (rounding) |
| Debate      | Medium  | **0.04375** | **0.0437** | **Truncated** |
| Debate      | Hard    | 0.0896    | 0.0896   | Exact  |

**Note:** Debate-medium computes to 0.04375, which rounds to 0.0438 (round-half-up) but is reported as 0.0437 (truncation). Difference: 0.0001. Negligible but worth standardizing rounding policy.

---

## 4. Accuracy Monotonicity

**Verdict: PASS**

### Monotonicity by difficulty (trivial > easy > medium > hard):

| Topology     | Trivial | Easy  | Medium | Hard  | Monotonic? |
|-------------|---------|-------|--------|-------|-----------|
| Flat        | 0.941   | 0.862 | 0.618  | 0.356 | Yes       |
| Hierarchical| 0.953   | 0.900 | 0.753  | 0.544 | Yes       |
| Debate      | 0.959   | 0.923 | 0.794  | 0.611 | Yes       |

### Ordering by topology (debate >= hierarchical >= flat):

| Tier    | Flat  | Hier  | Debate | Ordered? |
|---------|-------|-------|--------|----------|
| Trivial | 0.941 | 0.953 | 0.959  | Yes      |
| Easy    | 0.862 | 0.900 | 0.923  | Yes      |
| Medium  | 0.618 | 0.753 | 0.794  | Yes      |
| Hard    | 0.356 | 0.544 | 0.611  | Yes      |

---

## 5. Seed Consistency

**Verdict: PASS (means match), WARNING (CI widths may be under-estimated)**

### Seed Mean Verification

All 12 topology-tier cells were checked. The mean of the 3 seed values matches the reported mean to within rounding in every case. All CIs are perfectly symmetric around the mean.

| Topology | Tier    | Seeds               | Computed Mean | Reported Mean | CI Half-Width |
|----------|---------|---------------------|---------------|---------------|---------------|
| Flat     | Trivial | 0.938, 0.950, 0.935 | 0.94100       | 0.941         | 0.019         |
| Flat     | Easy    | 0.846, 0.877, 0.862 | 0.86167       | 0.862         | 0.031         |
| Flat     | Medium  | 0.611, 0.635, 0.608 | 0.61800       | 0.618         | 0.039         |
| Flat     | Hard    | 0.333, 0.378, 0.356 | 0.35567       | 0.356         | 0.042         |
| Hier     | Trivial | 0.950, 0.963, 0.946 | 0.95300       | 0.953         | 0.015         |
| Hier     | Easy    | 0.892, 0.908, 0.900 | 0.90000       | 0.900         | 0.024         |
| Hier     | Medium  | 0.741, 0.765, 0.753 | 0.75300       | 0.753         | 0.035         |
| Hier     | Hard    | 0.533, 0.567, 0.533 | 0.54433       | 0.544         | 0.043         |
| Debate   | Trivial | 0.956, 0.969, 0.951 | 0.95867       | 0.959         | 0.014         |
| Debate   | Easy    | 0.915, 0.931, 0.923 | 0.92300       | 0.923         | 0.020         |
| Debate   | Medium  | 0.782, 0.806, 0.794 | 0.79400       | 0.794         | 0.033         |
| Debate   | Hard    | 0.600, 0.633, 0.600 | 0.61100       | 0.611         | 0.040         |

### CI Width Spot-Check

For `flat/hard` with seeds [0.333, 0.378, 0.356]:
- Sample SD = 0.02252, SE = 0.01300
- t-interval (df=2, alpha=0.05): half-width = 4.303 * 0.01300 = **0.056**
- Reported half-width: **0.042**
- The reported CI is ~25% narrower than a standard 95% t-interval for n=3.

**Warning:** CI computation method is not documented. The reported CIs appear narrower than standard t-distribution 95% CIs for n=3 (df=2). They may use a z-interval, bootstrap, or Bayesian credible interval. The paper should document the CI method.

---

## 6. Routing Policy Costs

**Verdict: PARTIAL PASS**

### Cross-policy consistency checks:

| Check | Expected | Actual | Match? |
|-------|----------|--------|--------|
| ParetOrch between flat and debate | 1.1424 < x < 3.1990 | 1.4939 | Yes |
| always_debate / always_flat | 2.8 | 3.1990 / 1.1424 = 2.7999 | Yes |
| cost_reduction_vs_debate | 1 - (1.4939/3.1990) | 0.5330 vs 0.5334 | Close (0.0004 rounding) |
| cost_reduction_vs_dit_accuracy | 1 - (1.4939/2.8912) | 0.4833 vs 0.4835 | Close (0.0002 rounding) |

### Sum-of-averages cross-check:

Using tier-average costs * tier counts to estimate always_flat total:
`17*0.0029 + 19*0.0067 + 24*0.0156 + 24*0.0320 = 1.3190`

Reported always_flat total: **1.1424**

**Discrepancy: 1.3190 vs 1.1424 (15.5% higher than reported)**

This cannot be verified without per-task cost data for all 84 tasks. The tier-average costs in `topology_cost_models` appear to be higher than the actual per-task costs used in the routing experiments. This may be because:
1. The cost model averages include overhead/variance not present in all tasks, or
2. The per-task costs vary and the averages reported in the cost model overestimate.

The internal ratio `always_debate / always_flat = 2.8` is exact, which is consistent with the uniform multiplier model.

---

## 7. Quality Retention

**Verdict: PASS**

```
paretorch total_correct / always_debate total_correct = 65.25 / 68.25
= 0.77678571 / 0.8125 = 0.956034...
```

Reported quality_retention: **0.9560**

Using the underlying integer-correct counts (65.25/84 and 68.25/84) yields 0.9560 when rounded to 4 decimal places. The displayed accuracy of 0.7768 is a rounded form; the ratio computed from it (0.7768/0.8125 = 0.9561) differs by 0.0001 due to rounding of the intermediate value.

---

## 8. Ablation Internal Consistency

**Verdict: PARTIAL FAIL**

### Cost ordering (all ablations > full ParetOrch):

| Variant                    | Cost   | > 1.4939? | Delta Cost |
|---------------------------|--------|-----------|------------|
| Full ParetOrch            | 1.4939 | --        | --         |
| no_pareto_optimization    | 1.5832 | Yes       | +6.0%  (computed: +5.98%)  |
| no_cost_model             | 1.6481 | Yes       | +10.3% (computed: +10.32%) |
| no_dit_features           | 1.7823 | Yes       | +19.3% (computed: +19.31%) |
| random_within_threshold   | 2.0145 | Yes       | +34.8% (computed: +34.85%) |

All ablation costs exceed full ParetOrch. `random_within_threshold` has the highest cost. Cost deltas verified. **PASS**

### Accuracy deltas (expressed as relative %, not percentage points):

| Variant                 | Accuracy | Delta (relative) | Reported | Match? |
|------------------------|----------|-------------------|----------|--------|
| no_dit_features        | 0.7560   | -2.68%            | -2.7%    | Yes (rounding) |
| no_cost_model          | 0.7738   | -0.39%            | -0.4%    | Yes (rounding) |
| no_pareto_optimization | 0.7619   | -1.92%            | -1.9%    | Yes (rounding) |
| random_within_threshold| 0.7798   | +0.39%            | +0.4%    | Yes (rounding) |

**PASS**

### cost_per_correct values:

**FAIL** -- The `cost_per_correct` field in the ablation section is inconsistent with `cost / (accuracy * 84)`:

| Variant                 | Cost   | Accuracy | Expected correct | Expected c/c | Reported c/c | Match? |
|------------------------|--------|----------|------------------|-------------|-------------|--------|
| full_paretorch         | 1.4939 | 0.7768   | 65.25            | 0.0229      | 0.0229      | Yes    |
| no_dit_features        | 1.7823 | 0.7560   | 63.50            | 0.0281      | 0.0236      | **NO** |
| no_cost_model          | 1.6481 | 0.7738   | 65.00            | 0.0254      | 0.0213      | **NO** |
| no_pareto_optimization | 1.5832 | 0.7619   | 64.00            | 0.0247      | 0.0208      | **NO** |
| random_within_threshold| 2.0145 | 0.7798   | 65.50            | 0.0308      | 0.0258      | **NO** |

All four ablation `cost_per_correct` values are 16--19% lower than what the formula `total_cost / total_correct` would produce. These values appear to have been computed using a different formula (possibly `total_cost / 84` scaled by some factor, or a per-correct-task average cost rather than total/correct). This needs correction or documentation.

---

## 9. DIT Feature Ranges

**Verdict: PASS**

All D, I, T values across 84 tasks are in [0.0, 1.0]. No out-of-range values found.

### Per-tier value ranges:

| Tier    | D range     | I range     | T range     | Avg DIT magnitude |
|---------|-------------|-------------|-------------|-------------------|
| Trivial | [0.0, 0.1]  | [0.0, 0.1]  | [0.0, 0.1]  | Low (~0.05)       |
| Easy    | [0.1, 0.3]  | [0.1, 0.3]  | [0.0, 0.2]  | Low-mod (~0.20)   |
| Medium  | [0.4, 0.6]  | [0.3, 0.5]  | [0.1, 0.5]  | Moderate (~0.42)  |
| Hard    | [0.6, 0.9]  | [0.5, 0.8]  | [0.2, 0.7]  | High (~0.63)      |

The DIT values show clear stratification by difficulty tier with appropriate separation. Some overlap exists at tier boundaries (e.g., medium D goes up to 0.6 and hard D starts at 0.6), which is realistic.

---

## 10. Per-Task Sample Data

**Verdict: PASS (with one anomaly)**

For all 12 sample tasks (3 per tier):
- All accuracy arrays contain only 0 or 1: **PASS**
- All cost values are positive: **PASS**
- All token values are positive integers: **PASS**

### Anomaly: T053 Hierarchical Token/Cost Inversion

| Seed | Tokens | Cost   |
|------|--------|--------|
| 1    | 12449  | 0.0710 |
| 2    | 13198  | 0.0752 |
| 3    | 13812  | 0.0729 |

Seed 3 has more tokens (13812) than seed 2 (13198) but lower cost (0.0729 vs 0.0752). This is inconsistent unless the input/output token split changes dramatically between seeds (input tokens are 4x cheaper than output). Plausible but unusual -- should be verified.

For all other tasks, the ordering of tokens and costs across seeds is consistent.

---

## Summary of Findings

| #  | Check                       | Verdict      | Severity  |
|----|----------------------------|-------------|-----------|
| 1  | Task count (84)            | PASS        | --        |
| 2  | Cost multipliers (2.1/2.8) | PASS        | --        |
| 3  | Cost per task USD formula  | PASS        | Minor (1 truncation) |
| 4  | Accuracy monotonicity      | PASS        | --        |
| 5  | Seed mean consistency      | PASS        | --        |
| 5b | CI width calibration       | WARNING     | Medium    |
| 6a | ParetOrch between flat/debate | PASS     | --        |
| 6b | always_flat sum from averages | UNVERIFIABLE | Medium |
| 7  | Quality retention          | PASS        | --        |
| 8a | Ablation cost ordering     | PASS        | --        |
| 8b | Ablation cost_per_correct  | **FAIL**    | **High**  |
| 9  | DIT feature ranges         | PASS        | --        |
| 10 | Per-task sample data       | PASS        | Minor (1 anomaly) |

### Action Items

1. **[HIGH] Fix ablation `cost_per_correct` values.** Four of five ablation entries have `cost_per_correct` values inconsistent with `total_cost / (accuracy * 84)`. Either correct the values or document the alternative formula used.

2. **[MEDIUM] Document CI computation method.** The reported 95% CIs are narrower than standard t-distribution intervals for n=3. State whether bootstrap, z-interval, or another method was used.

3. **[MEDIUM] Reconcile tier-average costs with routing totals.** The `topology_cost_models` tier averages, when summed over 84 tasks, yield $1.32 for always_flat, but the routing experiment reports $1.14. Either note that the cost model is approximate or provide individual task costs.

4. **[LOW] Standardize rounding.** Debate-medium cost rounds to 0.0438 (round-half-up) but is reported as 0.0437 (truncation). Use consistent rounding throughout.

5. **[LOW] Verify T053 hierarchical seed 3.** Token count (13812) exceeds seed 2 (13198) but cost (0.0729) is less than seed 2 (0.0752). Confirm the input/output split justifies this inversion.
