# Cross-Section Consistency Audit

**Paper:** ParetOrch: Cost-Quality Pareto Optimization for Multi-Agent Topology Selection
**File:** `/Users/rj/eb-paper-claude/paper/output/main.tex`
**Date:** 2026-03-17
**Auditor:** Automated consistency checker

---

## 1. Number Consistency

### 1.1 Primary Result: 53.3% Cost Reduction

| Section | Line | Value | Verdict |
|---------|------|-------|---------|
| Abstract | 29 | 53.3% | -- |
| Introduction | 53 | 53.3% | -- |
| Results (table) | 225 | 53.3% | -- |
| Results (text) | 230 | 53.3% | -- |
| Conclusion | 297 | 53.3% | -- |

**Verification from table:** 1 - (1.49 / 3.20) = 53.4%
**Verdict: FLAG** -- Paper rounds to 53.3% everywhere; raw calculation gives 53.4%. Consistent internally but 0.1pp rounding discrepancy vs. table math.

---

### 1.2 Primary Result: 95.6% Quality Retention

| Section | Line | Value | Verdict |
|---------|------|-------|---------|
| Abstract | 29 | 95.6% | -- |
| Introduction | 53 | 95.6% | -- |
| Results (table) | 225 | 95.6% | -- |
| Conclusion | 297 | 95.6% | -- |

**Verification:** 77.7 / 81.3 = 95.57% rounds to 95.6%.
**Verdict: PASS**

---

### 1.3 Total Runs: 2,016

| Section | Line | Value |
|---------|------|-------|
| Abstract | 29 | 2,016 |
| Introduction | 59 | 2,016 |
| Experiments | 199 | 2,016 |
| Conclusion | 297 | 2,016 |

**Verification:** 84 tasks x 3 topologies x 3 seeds = 756 topology runs; + 5 policies x 84 tasks x 3 seeds = 1,260 policy runs; 756 + 1,260 = 2,016.
**Verdict: PASS**

---

### 1.4 Cost Per Correct Answer: $0.047 to $0.023

| Section | Line | Values |
|---------|------|--------|
| Introduction | 53 | $0.047 to $0.023 |
| Results (table) | 223-225 | Debate $0.047, ParetOrch $0.023 |
| Results (text) | 230 | $0.047 to $0.023, 51.1% reduction |

**Verification:** (0.047 - 0.023) / 0.047 = 51.06%, rounds to 51.1%.
**Verdict: PASS**

---

### 1.5 DIT Crossover at ~0.35

| Section | Line | Value |
|---------|------|-------|
| Abstract | 29 | ~0.35 |
| Introduction | 44, 61 | 0.35 |
| Method | 136 | 0.35 |
| Results | 237 | ~0.35 |
| Discussion | 281 | ~0.35 |
| Conclusion | 297 | ~0.35 |

**Verdict: PASS**

---

### 1.6 Debate Cost Multiplier: 2.8x

| Section | Line | Value |
|---------|------|-------|
| Introduction | 40 | 2.8x |
| Method | 118 | alpha_debate ~ 2.8; observed 2.80 +/- 0.11 |
| Experiments | 191 | ~2.8x |

**Verdict: PASS**

---

### 1.7 Hierarchical Cost Multiplier: 2.1x

| Section | Line | Value |
|---------|------|-------|
| Method | 118 | alpha_hier ~ 2.1; observed 2.10 +/- 0.08 |
| Experiments | 190 | ~2.1x |

**Verdict: PASS**

---

### 1.8 DIT Ablation: 19.3% Cost Increase

| Section | Line | Value |
|---------|------|-------|
| Abstract | 29 | 19.3% |
| Results (fig caption) | 259 | 19.3% |
| Results (text) | 263 | +19.3% |

**Verdict: PASS**

---

### 1.9 Trivial-Task Gap: 1.8pp, p = 0.41

| Section | Line | Value |
|---------|------|-------|
| Introduction | 41 | 1.8pp, p = 0.41 |
| Results | 241 | 2pp, p = 0.41 |
| Results (robustness) | 267 | 1.8pp, p = 0.41 |

**Note:** Line 241 says "within 2pp" while lines 41 and 267 say "1.8 percentage points." These are compatible (1.8pp is within 2pp) but not identical phrasing.
**Verdict: PASS** (compatible)

---

### 1.10 Hard-Task Gap: 25.5pp (35.6% flat vs 61.1% debate)

| Section | Line | Value |
|---------|------|-------|
| Introduction | 41 | 35.6% flat, 61.1% debate, 25.5pp gap |
| Method | 136 | debate outperforms flat by 25.5 points at DIT > 0.70 |
| Results | 241 | 25.5pp gap on hard tasks |

**Verdict: PASS**

---

### 1.11 48.3% Cost Savings vs DIT Accuracy-Only Router

| Section | Line | Value |
|---------|------|-------|
| Introduction | 53 | 48.3% |
| Results | 230 | 48.3% |

**Verification from table:** 1 - (1.49 / 2.89) = 48.4%.
**Verdict: FLAG** -- Paper says 48.3%; table math gives 48.4%. Minor rounding.

---

### 1.12 Always-Flat Cost Reduction in Table

**Table value (line 220):** 64.3%
**Verification:** 1 - (1.14 / 3.20) = 64.4%
**Verdict: FLAG** -- 0.1pp rounding difference.

---

### 1.13 DIT Accuracy-Only Cost Reduction in Table

**Table value (line 222):** 9.6%
**Verification:** 1 - (2.89 / 3.20) = 9.7%
**Verdict: FLAG** -- 0.1pp rounding difference.

---

### 1.14 FAIL: Discussion Daily/Annual Cost Savings

**Discussion (line 277):** "saves 53% of total cost. For 10,000 tasks/day at debate pricing, this is ~$160/day or ~$58K/year."

**Introduction (line 40):** "always-debate costs approximately $380" for 10,000 tasks/day.

**Expected calculation:** 53.3% of $380/day = **$202.54/day** or **$73.9K/year**.

**Actual Discussion claim:** $160/day, $58K/year.

$160/day implies only 42.1% savings, not 53.3%. The $58K/year is internally consistent with $160/day ($160 x 365 = $58,400) but both figures contradict the 53.3% savings claim and the Introduction's $380/day debate baseline.

**Verdict: FAIL** -- Direct numerical contradiction. Discussion savings ($160/day, $58K/year) are inconsistent with the claimed 53.3% reduction of the $380/day debate baseline (should be ~$203/day, ~$74K/year).

---

### 1.15 Seed-Level Results vs Aggregate

| Seed | Cost Red. | Quality Ret. |
|------|-----------|-------------|
| 42 | 53.6% | 93.5% |
| 137 | 53.1% | 98.2% |
| 2024 | 53.3% | 95.1% |
| **Mean** | **53.33%** | **95.6%** |

**Reported aggregate:** 53.3% cost reduction, 95.6% quality retention.
**Verdict: PASS**

---

### 1.16 Task Count Consistency

| Section | Detail | Value |
|---------|--------|-------|
| Experiments (line 184) | Tier breakdown | 17 + 19 + 24 + 24 = 84 |
| Experiments (line 184) | Categories | 12 listed, 12 counted |
| Method (line 118) | Topology runs | 756 (= 84 x 3 x 3) |

**Verdict: PASS**

---

## 2. Terminology Consistency

### 2.1 DIT Definition

| Section | Line | Phrasing |
|---------|------|----------|
| Abstract | 29 | "DIT (Decomposability, Iterativeness, Tool Diversity)" |
| Introduction | 44 | "DIT (Decomposability, Iterativeness, Tool Diversity)" |
| Method | 102 | "D measures Decomposability, I measures Iterativeness, and T measures Tool diversity" |

**Verdict: FLAG** -- "Tool Diversity" (capital D) in Abstract/Intro vs "Tool diversity" (lowercase d) in Method (line 102). Should be consistent.

---

### 2.2 ParetOrch Capitalization

Consistent throughout: always "ParetOrch" with capital P and capital O.
**Verdict: PASS**

---

### 2.3 Topology Names

Consistently "flat," "hierarchical," and "debate" (lowercase in running text, formatted in code as `\texttt{flat}`, `\texttt{hierarchical}`, `\texttt{debate}`).
**Verdict: PASS**

---

### 2.4 Quality Threshold Notation

Consistently uses tau symbol and "quality threshold" interchangeably. Never conflated with other thresholds.
**Verdict: PASS**

---

### 2.5 "DIT magnitude" Definition

Consistently defined as ||d|| = sqrt(D^2 + I^2 + T^2) (line 134). Referenced consistently as "DIT magnitude" elsewhere.
**Verdict: PASS**

---

## 3. Claim Consistency

### 3.1 Introduction Claims Supported by Results

| Intro Claim | Results Evidence | Match? |
|-------------|-----------------|--------|
| 53.3% cost reduction (line 53) | Table 1, line 225 | Yes |
| 95.6% quality retention (line 53) | Table 1, line 225 | Yes |
| 48.3% savings vs DIT acc-only (line 53) | Line 230 | Yes |
| 31.1% savings vs random (line 53) | Table: 1-(1.49/2.17)=31.3% | FLAG |
| Cost/correct $0.047 to $0.023 (line 53) | Table 1 | Yes |

**Verdict: FLAG** -- "31.1% vs random" (Intro line 53) computes to 31.3% from table values. Minor rounding.

---

### 3.2 Discussion Introduces Claim Not in Results

**Discussion (line 277):** "ParetOrch assigns 40% of tasks to flat topology"

This specific percentage (40%) does not appear in the Results section. Results mentions "100% of trivial tasks to flat" (line 241) and "70% to debate" for hard tasks (line 241), but the aggregate "40% to flat" figure is new in Discussion.

**Verdict: FLAG** -- New quantitative claim in Discussion not grounded in Results. Should either appear in Results first or be derivable from stated numbers.

---

### 3.3 Method Descriptions Match Experiments

- Method specifies logistic regression per topology (line 129): matches implied evaluation setup.
- Method specifies 7-dimensional feature expansion (line 134): not contradicted.
- Experiments specifies GPT-4o (2024-08-06) as base model (line 188): not contradicted elsewhere.
- Method cost multipliers (line 118) match Experiments topology descriptions (lines 189-191).

**Verdict: PASS**

---

### 3.4 Abstract Claims Match Body

Every quantitative claim in the Abstract (53.3%, 95.6%, 19.3%, ~0.35, tau=0.75, 84 tasks, 2016 runs) is supported by matching numbers in the body.
**Verdict: PASS**

---

### 3.5 Conclusion Claims Match Body

Conclusion (line 297) repeats: 84 tasks, 3-seed, 2016 runs, 53.3%, 95.6%, ~0.35. All match.
**Verdict: PASS**

---

## 4. Figure and Table References

### 4.1 All \ref{fig:} Citations Resolve

| Reference | Location | Label Defined? |
|-----------|----------|---------------|
| \ref{fig:framework} | Line 44 | Yes, line 50 |
| \ref{fig:crossover} | Line 241 | Yes, line 238 |
| \ref{fig:pareto} | Line 252 | Yes, line 249 |
| \ref{fig:ablation} | Line 263 | Yes, line 260 |

**Verdict: PASS**

---

### 4.2 Unreferenced Figures

**fig:cost_models** (label defined at line 124) is **never referenced** via `\ref{fig:cost_models}` anywhere in the text body. The figure exists (Section 3.2, line 120-125) but no sentence points the reader to it.

**Verdict: FAIL** -- Figure `fig:cost_models` (line 124) is a floating figure with no in-text reference. This will produce a LaTeX warning and is poor academic practice.

---

### 4.3 All \ref{tab:} Citations Resolve

| Reference | Location | Label Defined? |
|-----------|----------|---------------|
| \ref{tab:main} | Line 209 | Yes, line 214 |

**Verdict: PASS**

---

### 4.4 Figure File Existence

All five `\includegraphics` paths resolve to files on disk:

| LaTeX Path | File Exists? |
|------------|-------------|
| figures/fig1_framework.png | Yes |
| figures/fig2_cost_models.png | Yes |
| figures/fig4_crossover.png | Yes |
| figures/fig3_pareto_frontier.png | Yes |
| figures/fig5_ablation.png | Yes |

**Note:** Additional figure files exist on disk that are NOT referenced in the paper: `fig1_dit_space.png`, `fig2_main_results.png`, `fig3_crossover.png`, `fig4_routing_ablation.png`, `fig5_agent_control.png`. These appear to be from a different version of the paper. Not an error, but potential source of confusion.

**Verdict: PASS** (referenced files exist; extra unreferenced files are informational)

---

## Summary

| Category | PASS | FLAG | FAIL |
|----------|------|------|------|
| Number consistency | 12 | 5 | 1 |
| Terminology consistency | 4 | 1 | 0 |
| Claim consistency | 4 | 2 | 0 |
| Figure/Table references | 3 | 0 | 1 |
| **Total** | **23** | **8** | **2** |

### FAIL Items (Must Fix)

1. **Discussion daily/annual savings (line 277):** Claims ~$160/day and ~$58K/year savings, but 53.3% of the $380/day always-debate baseline yields ~$203/day and ~$74K/year. Either the percentages or the dollar figures are wrong. Fix the Discussion to say "~$200/day or ~$74K/year" OR recalculate the savings percentage.

2. **Unreferenced Figure (line 124):** `fig:cost_models` has a `\label` but no `\ref` in text. Add a sentence in Section 3.2 referencing it (e.g., "Figure~\ref{fig:cost_models} shows token cost per task by topology and tier.") or remove the figure.

### FLAG Items (Should Review)

1. **Rounding: 53.3% vs 53.4%** (lines 29, 53, 225, 230, 297) -- Table math gives 53.4%. If CIs are computed before rounding, 53.3% may be correct, but verify.
2. **Rounding: 48.3% vs 48.4%** (lines 53, 230) -- Table math gives 48.4%.
3. **Rounding: 31.1% vs 31.3%** (line 53) -- Table math gives 31.3%.
4. **Rounding: 64.3% vs 64.4%** (line 220) -- Table math gives 64.4%.
5. **Rounding: 9.6% vs 9.7%** (line 222) -- Table math gives 9.7%.
6. **Capitalization: "Tool Diversity" vs "Tool diversity"** (lines 29 vs 102) -- Pick one and use consistently.
7. **New claim in Discussion: "40% of tasks to flat"** (line 277) -- Not stated in Results section. Either add to Results or derive explicitly.
8. **Rounding note:** All five rounding FLAGs are 0.1pp discrepancies. If percentages were computed from higher-precision intermediate values (before rounding costs to 2 decimal places), they may be correct. Recommend adding a footnote or verifying from raw data.
