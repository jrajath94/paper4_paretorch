# Figure-Text Consistency Audit

**Paper:** ParetOrch: Cost-Quality Pareto Optimization for Multi-Agent Topology Selection
**Date:** 2026-03-17
**Files audited:**
- `paper/output/main.tex`
- `paper/scripts/generate_pareto_figures.py`
- `paper/experiments/pareto_results.json`

---

## Severity Legend

- **CRITICAL** -- Data mismatch that could mislead readers or reviewers
- **MODERATE** -- Inconsistency that undermines clarity or reproducibility
- **MINOR** -- Cosmetic or rounding issue unlikely to mislead

---

## 1. Figure 1 (Framework Overview)

**Caption (line 49):** "ParetOrch framework overview. A task enters the DIT feature extractor, which produces a compact (D, I, T) representation. The Pareto-aware router estimates per-topology success probability and cost, then selects the cheapest topology meeting quality threshold tau. Bold arrow indicates selected topology."

**Text reference (line 44):** "Figure 1 illustrates the framework."

| Check | Status | Detail |
|-------|--------|--------|
| Caption describes script content | PASS | Script renders Task -> DIT Extract -> Router -> Topologies -> Result flow |
| "Bold arrow indicates selected topology" | PASS | Script draws thick (lw=1.5) black arrow to Flat (selected) vs thin gray to others |
| Text context matches figure | PASS | Section 1 describes the 3-stage pipeline; figure illustrates it |

### MODERATE: Inconsistent cost labels on topology boxes

The script hardcodes cost labels on the three topology boxes:

| Topology | Script label | Trivial (JSON) | Easy (JSON) | Medium (JSON) | Hard (JSON) |
|----------|-------------|----------------|-------------|---------------|-------------|
| Flat     | $0.003      | $0.0029        | $0.0067     | $0.0156       | $0.0320     |
| Hier.    | $0.016      | $0.0060        | $0.0141     | $0.0328       | $0.0672     |
| Debate   | $0.044      | $0.0080        | $0.0188     | $0.0437       | $0.0896     |

- Flat $0.003 matches trivial tier ($0.0029 rounded).
- Hier. $0.016 matches no tier (closest: medium flat $0.0156 or easy hier $0.0141).
- Debate $0.044 matches medium tier ($0.0437 rounded).

**Impact:** The three cost labels are drawn from different tiers or are unexplained averages. Since this is a conceptual diagram, impact is limited, but a reviewer could flag the inconsistency.

**Fix:** Use all-trivial or all-average costs, or remove cost labels from the conceptual figure (they already appear properly in Figure 2).

---

## 2. Figure 2 (Cost Models)

**Caption (line 123):** "Token cost per task by topology and difficulty tier. Flat costs $0.003 per trivial task vs. debate at $0.008, a 2.8x gap for 1.8pp accuracy gain."

| Check | Status | Detail |
|-------|--------|--------|
| Script data keys | PASS | Pulls `data['topology_cost_models'][topo]['cost_per_task_usd'][tier]` -- all keys exist in JSON |
| Caption: flat trivial = $0.003 | PASS | JSON: $0.0029, rounds to $0.003 |
| Caption: debate trivial = $0.008 | PASS | JSON: $0.0080, matches |
| Caption: 2.8x gap | PASS | $0.008 / $0.0029 = 2.76x; overhead multiplier = 2.8x |
| Caption: 1.8pp accuracy gain | PASS | Debate trivial 95.9% - Flat trivial 94.1% = 1.8pp |
| Cost multipliers in text (line 118) | PASS | alpha_flat=1.0, alpha_hier=2.1, alpha_debate=2.8 match JSON overhead_multiplier values |

**Verdict:** Clean. No issues found.

---

## 3. Figure 3 (Pareto Frontier)

**Caption (line 252):** "Cost-quality Pareto frontier. Each ParetOrch point corresponds to a different quality threshold tau. The frontier reveals smooth tradeoffs with diminishing returns."

### Baseline positions (cost, accuracy) vs JSON

| Baseline | Script key | JSON cost | JSON acc | Correct? |
|----------|-----------|-----------|----------|----------|
| Always-Flat | always_flat | 1.1424 | 68.75% | PASS |
| Random | random_routing | 2.1707 | 75.0% | PASS |
| DIT Acc-Only | dit_accuracy_only | 2.8912 | 80.36% | PASS |
| Always-Debate | always_debate | 3.1990 | 81.25% | PASS |
| ParetOrch (tau=0.75) | paretorch | 1.4939 | 77.68% | PASS |

### MODERATE: Missing Majority-Class baseline

The text (line 199) claims **five** baseline routing policies: "Always-Flat, Always-Debate, Random, DIT Accuracy-Only, and **Majority-Class**." However:
- Majority-Class does not appear in `pareto_results.json`
- Majority-Class does not appear in Table 1 (lines 220-231)
- Majority-Class is not plotted in the Pareto frontier figure
- The script only plots 4 baselines + ParetOrch

**Impact:** A reviewer comparing Section 4.3's claim of "five routing policies" against Table 1 and Figure 3 will find only four baselines. This is a clear discrepancy.

**Fix:** Either add Majority-Class data to JSON/table/figure, or remove it from the text.

### MINOR: Figure annotation says "53.3%" vs text "53.4%"

The script (line 194) annotates the ParetOrch star with "53.3% cost reduction." The abstract (line 29), Table 1 description (line 234), and all other text references say "53.4%." JSON: `cost_reduction_vs_debate = 0.5334` = 53.34%, which rounds to 53.3% (2 sig figs) or 53.4% (1 decimal). The figure and text use different rounding conventions.

**Fix:** Change script annotation from "53.3%" to "53.4%" for consistency with the paper text.

### tau=0.75 highlighting

| Check | Status | Detail |
|-------|--------|--------|
| tau=0.75 annotated on frontier curve | PASS | Script annotates tau values [0.65, 0.75, 0.85, 0.90] on the frontier |
| tau=0.75 plotted as star marker | PASS | Star marker (s=200) with distinct color |
| Frontier data source | PASS | Pulls from `data['pareto_frontier_analysis']['thresholds']` |

---

## 4. Figure 4 (Crossover)

**Caption (line 241):** "Topology performance vs. DIT magnitude. Below ||d|| approx 0.35, all topologies perform within 2pp; flat is Pareto-optimal. The gap widens with DIT magnitude."

### DIT magnitude x-axis values

| Tier | Script value | Text (line 186) | Match? |
|------|-------------|-----------------|--------|
| Trivial | 0.07 | 0.07 | PASS |
| Easy | 0.34 | 0.34 | PASS |
| Medium | 0.72 | 0.72 | PASS |
| Hard | 1.10 | 1.10 | PASS |

### Accuracy values per tier

| Tier | Topology | Script source | JSON mean | Correct? |
|------|----------|--------------|-----------|----------|
| Trivial | Flat | topology_accuracy_by_difficulty.flat.trivial.mean | 0.941 (94.1%) | PASS |
| Trivial | Hier | ...hierarchical.trivial.mean | 0.953 (95.3%) | PASS |
| Trivial | Debate | ...debate.trivial.mean | 0.959 (95.9%) | PASS |
| Hard | Flat | ...flat.hard.mean | 0.356 (35.6%) | PASS |
| Hard | Debate | ...debate.hard.mean | 0.611 (61.1%) | PASS |

### Crossover line at 0.35

Script: `ax.axvline(x=0.35, ...)` -- **PASS**

### CRITICAL: Caption claim "within 2pp below 0.35" contradicted by easy-tier data

The caption states: "Below ||d|| approx 0.35, all topologies perform within 2pp."

The easy tier has mean DIT magnitude = 0.34 (below 0.35). At this tier:
- Flat: 86.2%, Debate: 92.3% -- **gap = 6.1pp**

This is 3x larger than the claimed 2pp. The "within 2pp" claim holds only for the trivial tier (DIT = 0.07, gap = 1.8pp).

The same claim appears in the body text (line 136): "on low-magnitude tasks (||d|| < 0.35), all three topologies achieve similar success rates (within 2pp)."

**Impact:** A central claim of the paper (the crossover insight) rests on this threshold. If the easy tier at DIT=0.34 already shows a 6.1pp gap, the crossover is not at 0.35 -- it is somewhere between 0.07 and 0.34.

**Nuance:** The text may intend this claim at the individual-task level, not the tier-average level. But with only 4 tier-level data points, the tier-level evidence does not support the claim. The crossover at 0.35 falls between the trivial (0.07) and easy (0.34) data points with no observations in between.

**Fix options:**
1. Change the crossover threshold to ~0.20 (midpoint between trivial and easy means), which better reflects the data
2. Weaken the claim: "Below ||d|| approx 0.10, all topologies perform within 2pp" (only trivial tier)
3. Add individual-task-level evidence to justify the 0.35 threshold

---

## 5. Figure 5 (Ablation)

**Caption (line 263):** "Ablation study at tau = 0.75. DIT features contribute the largest margin: removing them increases cost by 19.3%."

### Script data keys vs JSON

| Variant | Script key | JSON key | Exists? |
|---------|-----------|----------|---------|
| Full ParetOrch | full_paretorch | full_paretorch | PASS |
| No DIT Features | no_dit_features | no_dit_features | PASS |
| No Cost Model | no_cost_model | no_cost_model | PASS |
| No Pareto Optim. | no_pareto_optimization | no_pareto_optimization | PASS |
| Random Threshold | random_within_threshold | random_within_threshold | PASS |

### Cost delta labels (script computes `(cost[i] - cost[0]) / cost[0] * 100`)

| Variant | Cost | Script delta | JSON delta_cost | Match? |
|---------|------|-------------|----------------|--------|
| No DIT | 1.7823 | +19.3% | "+19.3%" | PASS |
| No Cost Model | 1.6481 | +10.3% | "+10.3%" | PASS |
| No Pareto | 1.5832 | +6.0% | "+6.0%" | PASS |
| Random Thresh | 2.0145 | +34.8% | "+34.8%" | PASS |

### MINOR: Accuracy delta labels use pp but JSON delta_accuracy uses relative %

The script computes absolute pp differences. The JSON pre-computed delta_accuracy strings are relative percentages:

| Variant | Script pp delta | JSON delta_accuracy | Discrepancy |
|---------|----------------|-------------------|-------------|
| No DIT | -2.1pp | "-2.7%" | 0.6pp gap (relative vs absolute) |
| No Cost Model | -0.3pp | "-0.4%" | 0.1pp gap |
| No Pareto | -1.5pp | "-1.9%" | 0.4pp gap |
| Random Thresh | +0.3pp | "+0.4%" | 0.1pp gap |

The figure labels are computed correctly from raw values (absolute pp difference). The JSON delta strings use a different convention (relative %). The text (line 267) says "+0.4%" for random_within_threshold, matching the JSON string rather than what the figure displays (+0.3pp).

**Impact:** Low. The cost deltas (the primary message) are consistent. Accuracy deltas are secondary and the differences are small.

**Fix:** Standardize on pp (percentage points) everywhere, or label figure axes/annotations explicitly as "pp" vs "%".

---

## 6. All Figure Captions -- Summary

| Figure | Caption accuracy | Issues |
|--------|-----------------|--------|
| Fig 1 (framework) | Mostly accurate | Cost labels on topology boxes inconsistent with JSON (MODERATE) |
| Fig 2 (cost models) | Accurate | None |
| Fig 3 (pareto) | Accurate | Missing Majority-Class baseline (MODERATE); 53.3% vs 53.4% (MINOR) |
| Fig 4 (crossover) | **Misleading** | "Within 2pp below 0.35" not supported by easy-tier data at DIT=0.34 (CRITICAL) |
| Fig 5 (ablation) | Accurate | pp vs relative % convention mismatch (MINOR) |

---

## 7. Figure Numbering

### LaTeX figure order (determines auto-numbering)

| Position in .tex | Label | File included | LaTeX assigns |
|-----------------|-------|---------------|---------------|
| Line 46 | fig:framework | fig1_framework.png | Figure 1 |
| Line 120 | fig:cost_models | fig2_cost_models.png | Figure 2 |
| Line 238 | fig:crossover | **fig4_crossover.png** | **Figure 3** |
| Line 249 | fig:pareto | **fig3_pareto_frontier.png** | **Figure 4** |
| Line 260 | fig:ablation | fig5_ablation.png | Figure 5 |

### MODERATE: Figure filenames swapped relative to LaTeX numbering

- `fig3_pareto_frontier.png` is the 4th figure environment in the .tex file, so LaTeX numbers it **Figure 4**
- `fig4_crossover.png` is the 3rd figure environment, so LaTeX numbers it **Figure 3**

The filenames suggest pareto=3, crossover=4, but LaTeX renders crossover=3, pareto=4.

### Text reference order

| Section | Reference | LaTeX Figure # | Sequential? |
|---------|-----------|---------------|-------------|
| Sec 1 (line 44) | fig:framework | Figure 1 | -- |
| Sec 3.2 (line 118) | fig:cost_models | Figure 2 | PASS |
| Sec 5.2 (line 245) | fig:crossover | Figure 3 | PASS |
| Sec 5.3 (line 256) | fig:pareto | Figure 4 | PASS |
| Sec 5.4 (line 267 implied) | fig:ablation | Figure 5 | PASS |

References are sequential in the text. No forward-reference violations.

**Impact:** The rendered PDF will be correct (LaTeX auto-numbers). But the swapped filenames create confusion for anyone working with the source files or the script (which generates fig3 as pareto and fig4 as crossover).

**Fix:** Either rename the files to match LaTeX ordering (fig3_crossover, fig4_pareto) or swap the figure environments in the .tex so pareto appears before crossover.

---

## Issue Summary

| # | Severity | Figure | Issue |
|---|----------|--------|-------|
| 1 | CRITICAL | Fig 4 (crossover) | Caption claims "within 2pp below 0.35" but easy tier at DIT=0.34 has 6.1pp gap |
| 2 | MODERATE | Fig 3 (pareto) | Majority-Class baseline claimed in text (Sec 4.3) but absent from data, table, and figure |
| 3 | MODERATE | Fig 1 (framework) | Cost labels mix values from different difficulty tiers |
| 4 | MODERATE | Fig 3/4 | Filenames (fig3=pareto, fig4=crossover) swapped relative to LaTeX auto-numbering |
| 5 | MINOR | Fig 3 (pareto) | Annotation "53.3%" vs text "53.4%" rounding inconsistency |
| 6 | MINOR | Fig 5 (ablation) | Accuracy deltas use pp in figure but relative % in JSON/text |

**Bottom line:** 1 critical issue (crossover claim not supported by tier-level data), 3 moderate issues (missing baseline, inconsistent labels, swapped filenames), 2 minor rounding/convention issues. The cost-side claims are consistently supported. The quality-side crossover claim at DIT ~0.35 needs the most attention.
