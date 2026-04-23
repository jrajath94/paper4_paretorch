# NeurIPS 2026 Peer Review Council Report

**Paper:** ParetOrch: Cost-Quality Pareto Optimization for Multi-Agent Topology Selection
**Venue:** NeurIPS 2026
**Review Round:** 1 (Initial)
**Date:** 2026-03-17
**Reviewers:** 5 independent personas

---

## Reviewer 1: Methodology Skeptic

**Expertise:** Senior ML researcher, 15 years experience. Focus on technical rigor, formalism correctness, assumptions validity.

### Scores

| Criterion     | Score | Weight |
|---------------|-------|--------|
| Quality       | 5     | 40%    |
| Clarity       | 7     | 15%    |
| Originality   | 6     | 15%    |
| Significance  | 5     | 30%    |
| **Weighted**  | **5.45** |     |
| Confidence    | 4/5   |        |

### Summary

The paper frames topology-level routing as a Pareto-constrained optimization problem and demonstrates cost savings on a custom 84-task benchmark. While the formulation is clean, the experimental methodology has multiple concerning gaps: costs are simulated rather than measured, the quality predictor is trained and tested on the same task set without cross-validation reporting, the "three random seeds" provide extremely thin statistical coverage (95% CI from n=3), and the task suite is author-designed with author-assigned DIT labels. The claimed 53.3% cost reduction is compelling if the methodology were airtight, but it rests on too many unvalidated assumptions for a top-venue paper.

### Strengths

- **Clean formalization.** The constrained optimization in Eq. 1 is well-posed. The feasibility-filter-then-cost-minimize procedure (Section 3.4) is elegant and provably selects a Pareto-optimal arm from the feasible set. This is a genuine contribution to the formulation.

- **Complete ablation.** Table 3 (Section 5.4) isolates DIT features (+19.3% cost), cost model (+10.3%), and Pareto optimization (+6.0%) independently. The "random-within-threshold" variant is an especially smart control -- it demonstrates that cost optimization is distinct from quality optimization.

- **Crossover insight is actionable.** The DIT magnitude 0.35 threshold (Section 5.2) provides a simple heuristic that practitioners can use without deploying the full ParetOrch pipeline. This kind of distilled takeaway adds practical value beyond the method itself.

- **Consistent numerical reporting.** The numbers in the abstract, introduction, results, and conclusion are internally consistent (verified: 53.3% cost reduction, 95.6% quality retention, $0.023 cost/correct all match across sections).

### Weaknesses

- **CRITICAL: Simulated costs, not real API billing.** Section 6.3 acknowledges "costs are simulated from token counting rather than real API billing," and the authors estimate this may underestimate deployment cost by 10-20%. This is not a minor caveat. The entire routing decision hinges on cost estimates. If overhead multipliers (alpha_flat=1.0, alpha_hier=2.1, alpha_debate=2.8) are deterministic constants applied to base token counts, the "cost model" is just multiplication by a fixed scalar. There is no learned cost component -- the cost model contributes 10.3% in ablation, but this is trivial since the overhead ratios are hardcoded. Real API costs include retries, rate-limit backoff, context window spillover, and variable latency -- none modeled here.

- **CRITICAL: n=3 seeds for 95% CIs is statistically inadequate.** The paper reports "95% confidence intervals" computed from 3 random seeds. With n=3, the t-distribution has only 2 degrees of freedom, yielding extremely wide and unreliable intervals. The reported CI width for accuracy is 3.8pp (Section 5.5), but with n=3, the actual 95% CI should use t_{0.025,2} = 4.303, making these intervals far less informative than they appear. Furthermore, presenting "mean +/- 95% CI" from 3 observations gives a false sense of precision. Standard practice would be at least 10 seeds, or bootstrap confidence intervals with many more replicates.

- **No train/test split for quality predictor.** The logistic regression quality predictor (Eq. 3) is fit on execution data from the 84 tasks, and evaluated on the same 84 tasks. There is no mention of cross-validation, held-out test set, or calibration evaluation. This is a classic overfitting concern: the predictor may simply memorize which tasks succeed under which topology. With 84 tasks and 7 features per topology, the model has 21 parameters for 252 data points (84 x 3 topologies) -- this is not an extreme ratio, but without held-out evaluation, we cannot assess generalization.

- **DIT features are author-assigned, not validated.** The DIT annotation protocol is described at a high level: "Each task is annotated with three DIT features on the [0,1] scale" (Section 4.1). Who performed the annotations? How was inter-annotator agreement measured? Are the DIT features objective or subjective? The entire routing framework depends on accurate DIT extraction, yet the paper treats DIT as given without validating that the extraction itself is reliable. Section 3 mentions DIT extraction "can be performed by a small classifier trained on task descriptions" but no such classifier is demonstrated.

- **The Pareto claim is overclaimed.** With only 3 discrete topologies, the "Pareto frontier" is at most 3 points for any given task. Calling this "Pareto optimization" overstates the sophistication -- it is actually just argmin over a filtered set of size <= 3. The framework scales to more topologies in principle, but as evaluated, it is a conditional minimum over 3 options.

### Questions for Authors

1. Can you provide leave-one-category-out cross-validation results for the quality predictor? With 12 task categories, this would give 12-fold CV and a meaningful generalization estimate.

2. The overhead multipliers alpha_hier=2.1 and alpha_debate=2.8 are reported as averages with standard deviations of 0.08 and 0.11. Have you analyzed whether alpha varies systematically with task difficulty beyond the Kruskal-Wallis test? Plotting alpha vs. DIT magnitude would strengthen the constant-multiplier assumption.

3. What happens to ParetOrch's cost savings under a realistic cost model that includes retries (e.g., 5-10% failure rate per LLM call), rate-limit delays, and context window overflow?

### Recommendation: **Borderline Reject (5)**

The formulation is clean and the ablation is thorough, but the experimental methodology has too many gaps for a NeurIPS paper. Simulated costs, n=3 seeds, no held-out evaluation of the predictor, and author-assigned DIT labels create compounding sources of potential error.

---

## Reviewer 2: Novelty Assessor

**Expertise:** Professor, agent systems expert. Focus on originality of contribution, positioning against prior art.

### Scores

| Criterion     | Score | Weight |
|---------------|-------|--------|
| Quality       | 6     | 15%    |
| Clarity       | 7     | 15%    |
| Originality   | 6     | 40%    |
| Significance  | 6     | 30%    |
| **Weighted**  | **6.15** |     |
| Confidence    | 4/5   |        |

### Summary

The paper identifies a real gap: existing cost-aware routing operates at the model level while multi-agent topology is fixed at design time. The contribution of connecting these two threads is genuine. However, the technical novelty is modest -- the method is a logistic regression classifier feeding into argmin over 3 options, which is straightforward engineering. The DIT framework, which is the most novel component, is borrowed from the authors' prior work. The strongest contribution is the empirical insight (crossover at DIT ~0.35), not the method itself.

### Strengths

- **Genuinely unoccupied intersection.** The related work section (Section 2) convincingly argues that cost-aware routing (FrugalGPT, RouteLLM, Hybrid LLM) operates at the model level, while multi-agent orchestration (MetaGPT, ChatDev, AutoGen) fixes topology. The claim that "no prior work routes between orchestration topologies based on cost-quality tradeoffs" appears correct after checking the cited literature.

- **Composability argument is compelling.** The insight that topology selection and model selection are orthogonal (Section 6.1: "FrugalGPT answers 'which model?' while ParetOrch answers 'which coordination pattern?'") is well-articulated and correctly identifies that these savings stack.

- **DAAO differentiation is clear.** The paper correctly distinguishes ParetOrch from DAAO (Su et al., 2025): DAAO operates at the operator level (which agent does what) while ParetOrch operates at the topology level (which coordination pattern to use). This is a meaningful distinction.

- **The crossover insight transfers.** The DIT magnitude ~0.35 threshold is model-agnostic in structure (Section 6.2) and connects to Simon's near-decomposability theory, giving it theoretical grounding beyond the specific experiment.

### Weaknesses

- **Limited technical novelty.** Strip away the framing and the method is: (1) compute 7 features from a 3D input, (2) run logistic regression, (3) take argmin. There is no new learning algorithm, no new optimization procedure, no new theoretical result. The Pareto selection (Section 3.4) is a feasibility filter followed by argmin -- this is the textbook solution to a constrained optimization over a discrete set of size 3. Calling this "Pareto-optimal routing" overstates the sophistication.

- **DIT framework is prior work.** Section 4.1 references "the DIT annotation protocol follows prior work on task characterization for agent routing" and Section 4.3 mentions "the accuracy-optimal DIT router from our prior work." This means the most novel component (DIT features) is not new to this paper. What is new is adding cost to the routing decision -- a useful but incremental extension.

- **Missing head-to-head with DAAO.** DAAO (Su et al., 2025) is the closest concurrent work, achieving "state-of-the-art results at 64% of inference cost." The paper distinguishes ParetOrch conceptually (topology vs. operator level) but does not compare empirically. Even a rough comparison on overlapping tasks would strengthen the positioning. Without it, we cannot assess whether ParetOrch's cost savings are competitive.

- **"First to" claim is strong.** The paper claims "the first framework that jointly optimizes cost and quality at the orchestration topology level." This is difficult to verify and risks overclaiming. The cost-aware routing literature is moving fast; OptLLM (Liu et al., 2024) already formulates multi-objective optimization with Pareto-dominant solutions. Extending their approach to topologies (rather than models) seems a natural step.

- **Three topologies limit generality.** The framework is evaluated with exactly three topologies (flat, hierarchical, debate). With |A|=3, the method is a simple conditional lookup. The paper claims extensibility to |A|>3 (Section 6.3) but does not demonstrate it. Adding even one more topology (e.g., pipeline, voting) would significantly strengthen the contribution.

### Questions for Authors

1. If DIT features are from prior work, what precisely is the algorithmic contribution of this paper beyond "add cost awareness to an existing DIT router"?

2. Can you run ParetOrch and DAAO on a shared subset of tasks (even 20-30 overlapping tasks) to provide an empirical comparison rather than only a conceptual distinction?

3. What is the routing accuracy of a simpler baseline: route to flat if task description is < 200 tokens, else debate? This would test whether the full DIT framework is necessary or whether a trivial proxy captures most of the benefit.

### Recommendation: **Borderline Accept (6)**

The gap identification is genuine and the empirical insight (crossover threshold) is valuable. But the technical contribution is thin -- a logistic regression and argmin over 3 options with borrowed DIT features. This reads more like a strong workshop paper or a systems contribution than a NeurIPS main-track paper.

---

## Reviewer 3: Clarity Reviewer

**Expertise:** Industry researcher, values practical impact. Focus on writing quality, figure clarity, reproducibility.

### Scores

| Criterion     | Score | Weight |
|---------------|-------|--------|
| Quality       | 6     | 15%    |
| Clarity       | 8     | 40%    |
| Originality   | 6     | 15%    |
| Significance  | 7     | 30%    |
| **Weighted**  | **6.90** |     |
| Confidence    | 3/5   |        |

### Summary

This is a well-written paper with a clear arc from problem to method to evaluation. The figures are readable and informative, notation is consistent throughout, and the abstract accurately represents the paper's content. The practical framing (dollar savings per day, single-knob interface) makes the contribution accessible to practitioners. Minor issues include some figure-text inconsistencies and missing details on the DIT extraction process.

### Strengths

- **Exceptional practical framing.** The opening paragraph immediately grounds the problem in concrete systems (MetaGPT, ChatDev, AutoGen). The cost extrapolation ("$245 daily gap, or $89,000 annually") gives practitioners an immediate sense of relevance. The "cost per correct answer" metric (Section 4.4) is a genuinely useful efficiency measure.

- **Clean notation throughout.** The DIT vector d(x) = (D, I, T), topology set A, quality predictor q(a,x), cost model c(a,x), and threshold tau are introduced once and used consistently across all 6 sections. No symbol is overloaded or redefined.

- **Figure quality is strong.** Fig. 1 (framework overview) clearly conveys the pipeline. Fig. 2 (main results) has readable axis labels, value annotations on bars, and a clear legend. Fig. 3 (crossover) effectively illustrates the topology advantage inversion between high-D and high-I tasks. Fig. 4 (ablation) includes the random baseline as a dashed reference line. Fig. 5 (agent-count control) decomposes compute vs. structure contribution with annotated arrows -- pedagogically effective.

- **Well-structured results section.** The five-subsection results layout (main comparison, stratified analysis, Pareto frontier, ablation, robustness) is logical. Each subsection answers a distinct question. Tables and figures alternate appropriately.

- **Honest limitations.** Section 6.3 identifies six specific limitations, including "costs are simulated" and "synthetic tasks may not match production distributions." This is refreshingly direct.

### Weaknesses

- **CRITICAL: Figure-text data inconsistency.** The figure_metadata.json references 82 tasks across the benchmark while the paper text consistently states 84 tasks. The metadata shows "Easy (n=12)" and "Hard (n=70)" in fig2, summing to 82, while the text states the breakdown is 17 trivial + 19 easy + 24 medium + 24 hard = 84 with four tiers. Furthermore, the figure metadata shows different accuracy numbers: overall flat=28.9% (metadata) vs. 68.8% (Table 1), debate=65.4% (metadata) vs. 81.3% (Table 1). These are not minor rounding differences -- they suggest the figures were generated from a different dataset than the one described in the paper. This is a serious consistency issue that undermines trust in the results.

- **DIT extraction is a black box.** The paper repeatedly states that DIT features are "extracted" from tasks but never specifies how. Section 3 says "DIT extraction can be performed by a small classifier trained on task descriptions." Is this what was done? Or were DIT features hand-labeled? The 84-task JSON file shows DIT values that appear manually assigned (all are round numbers: 0.0, 0.1, 0.2, ..., 1.0). If hand-labeled, the paper should say so explicitly and discuss inter-annotator agreement.

- **Missing reproducibility details.** No code repository URL is provided (abstract says "[repository URL]" as a placeholder). No hyperparameters for the logistic regression are reported (regularization strength, convergence criteria). The debate topology uses "2 critique rounds" -- is this fixed or adaptive? The GPT-4o model version is specified (good), but no system prompt or temperature settings are given.

- **Annual cost extrapolation assumes 10,000 tasks/day.** The $89K annual savings figure (Introduction) assumes a specific workload volume that may or may not reflect typical deployments. The paper should note that this is illustrative rather than representative. The Discussion section uses a different figure ($58K/year) without explaining the discrepancy -- the Introduction says $89K (gap between always-debate and always-flat), while the Discussion says $58K (ParetOrch savings). These are different calculations but may confuse readers.

### Questions for Authors

1. Can you reconcile the figure metadata (82 tasks, 2 tiers, flat overall=28.9%) with the paper text (84 tasks, 4 tiers, flat overall=68.8%)? Were the figures generated from a different experiment run than the one reported in the tables?

2. Are the DIT features in pareto_tasks.json hand-labeled or classifier-extracted? If hand-labeled, who labeled them and was there any inter-annotator reliability assessment?

3. Will you release code and data before camera-ready? The placeholder "[repository URL]" in the abstract is acceptable for review but should be noted as a commitment.

### Recommendation: **Borderline Accept (6)**

The writing quality is strong and the practical framing is excellent. The figure-text inconsistency is concerning but likely fixable. The paper would benefit from explicit description of the DIT labeling process and concrete reproducibility artifacts.

---

## Reviewer 4: Domain Expert

**Expertise:** Multi-agent systems researcher. Focus on topology definitions, DIT validity, experiment design realism.

### Scores

| Criterion     | Score | Weight |
|---------------|-------|--------|
| Quality       | 5     | 30%    |
| Clarity       | 7     | 15%    |
| Originality   | 7     | 25%    |
| Significance  | 5     | 30%    |
| **Weighted**  | **5.80** |     |
| Confidence    | 5/5   |        |

### Summary

As someone who works on multi-agent coordination, I find the problem formulation compelling but the experimental design insufficiently realistic. The three "topologies" are highly stylized versions of what real multi-agent systems look like. The task suite is synthetic. The DIT dimensions, while intuitively reasonable, lack formal grounding beyond a citation to Simon (1962). The paper would be significantly stronger with evaluation on existing multi-agent benchmarks (GAIA, SWE-bench, MATH) using real multi-agent frameworks rather than simulated topologies on custom tasks.

### Strengths

- **Problem is real and underaddressed.** In my experience deploying multi-agent systems, topology selection is indeed ad-hoc. The cost difference between single-agent and multi-agent debate is substantial in practice (often 3-5x, consistent with the 2.8x reported here). The paper correctly identifies this as an optimization opportunity.

- **DIT decomposition is intuitive.** Decomposability, Iterativeness, and Tool Diversity capture meaningful task dimensions. The crossover insight (high-D favors hierarchical, high-I favors debate) aligns with my experience: hierarchical topologies excel when tasks can be cleanly partitioned, while debate excels when iterative refinement is key.

- **The crossover figure (Fig. 3) is the paper's strongest result.** The topology advantage inversion between high-D (hierarchical at 75%) and high-I (debate at 77%) tasks is a genuinely novel empirical finding. This is the kind of insight that practitioners can act on immediately.

- **Agent-count control experiment (Fig. 5) is well-designed.** Decomposing the multi-agent advantage into compute (33%) and structure (67%) components addresses the obvious confound that multi-agent systems simply use more tokens, not that coordination helps.

### Weaknesses

- **CRITICAL: Topology definitions are oversimplified.** The paper defines three topologies: flat (1 agent), hierarchical (coordinator + specialists), and debate (adversarial rounds + judge). Real multi-agent systems use far richer coordination patterns. MetaGPT uses a sequential pipeline with artifact passing, not a simple hierarchy. AutoGen supports dynamic conversation graphs that do not fit neatly into any of these three categories. ChatDev uses a waterfall with role-specific prompts. By collapsing all multi-agent coordination into three buckets, the paper loses the nuance that makes topology selection interesting.

- **CRITICAL: Synthetic tasks do not validate generalization.** The 84 tasks are author-designed to "span the DIT space." This is circular: tasks are designed with specific DIT properties, annotated with those same properties, and then the router trained on those properties performs well. A convincing evaluation would use an existing benchmark (GAIA, SWE-bench) and show that DIT features extracted from those tasks enable cost-effective routing. Without external validation, we cannot assess whether ParetOrch generalizes beyond its training distribution.

- **DIT dimensions lack formal grounding.** The paper cites Simon (1962) on near-decomposability, but this is a loose connection. Simon's theory concerns hierarchically near-decomposable systems with weak inter-module coupling -- it does not directly map to "Decomposability, Iterativeness, Tool Diversity." The I (Iterativeness) and T (Tool Diversity) dimensions have no theoretical basis cited. Why these three dimensions and not others (e.g., task length, domain specificity, required reasoning depth)?

- **No evaluation on standard benchmarks.** GAIA is cited as inspiration for task design but not used as an evaluation benchmark. This is a missed opportunity. Running ParetOrch on GAIA tasks with actual multi-agent frameworks (MetaGPT, AutoGen) would dramatically strengthen the paper. Similarly, SWE-bench tasks have natural DIT properties (decomposability varies with bug complexity) and could serve as an external validation set.

- **The "topology" is actually "agent count + interaction pattern."** Flat = 1 agent; hierarchical = 3-4 agents with delegation; debate = 3-4 agents with argumentation. The paper conflates two dimensions: number of agents and communication pattern. A proper topology study would control for agent count (e.g., 3 agents in both hierarchical and debate) and vary only the interaction pattern, or would include agent count as a feature in the cost model.

### Questions for Authors

1. Have you evaluated ParetOrch on any external benchmark (GAIA, SWE-bench, MATH)? Even a small-scale pilot (20 tasks from GAIA) with real multi-agent frameworks would address the generalization concern.

2. Why D, I, T specifically? Have you run experiments with alternative task characterization dimensions (e.g., reasoning depth, domain knowledge requirements) to validate that DIT is superior?

3. The agent-count control (Fig. 5) uses a 20-task hard subset. Why not run this control across all 84 tasks? The compute-vs-structure decomposition may differ at lower difficulty levels where the multi-agent advantage is smaller.

### Recommendation: **Borderline Reject (5)**

The problem is important and the framing is compelling, but the experimental design is too synthetic to support the paper's claims. Evaluation on standard benchmarks with real multi-agent frameworks would address this concern. As it stands, the paper demonstrates that if you design tasks with specific DIT properties and label them accordingly, a DIT-based router works -- which is closer to a tautology than an empirical finding.

---

## Reviewer 5: Ethics & Reproducibility

**Expertise:** Program committee member. Focus on broader impact, reproducibility, limitations honesty.

### Scores

| Criterion     | Score | Weight |
|---------------|-------|--------|
| Quality       | 6     | 20%    |
| Clarity       | 7     | 20%    |
| Originality   | 6     | 20%    |
| Significance  | 6     | 20%    |
| **Weighted**  | **6.25** (equal) |     |
| Confidence    | 3/5   |        |

### Summary

The paper is reasonably transparent about its limitations and avoids egregious overclaiming. The six-item limitations list (Section 6.3) is more honest than most submissions. However, the reproducibility gaps are concerning: no code release, simulated rather than real costs, and hand-labeled DIT features treated as ground truth. The broader impact discussion is absent -- a cost-optimization framework for multi-agent LLM systems has environmental implications (reduced compute = reduced carbon) and economic implications (potentially displacing human oversight of agent coordination) that deserve mention.

### Strengths

- **Unusually honest limitations section.** Section 6.3 explicitly lists six limitations including "costs are simulated from token counting rather than real API billing" and "synthetic tasks may not match production distributions." This is more transparent than many NeurIPS submissions, which tend to bury limitations in passing remarks.

- **Environmental co-benefit is implicit.** While not explicitly stated, a 53% reduction in API calls translates directly to reduced compute and energy consumption. This aligns with growing concerns about LLM inference costs and environmental impact.

- **Clear experimental scope.** The paper does not overgeneralize. Claims are scoped to "84 tasks, 3 topologies, GPT-4o" and the Discussion notes that results may not transfer to other models or topology sets (Section 6.3, items 3 and 6).

- **Three-seed replication is present.** While n=3 is thin, the fact that all primary results are replicated across 3 seeds and reported with CIs is better than single-run reporting, which remains unfortunately common.

### Weaknesses

- **No broader impact statement.** NeurIPS requires authors to discuss broader societal impact. Cost optimization for multi-agent LLM systems has implications for: (a) environmental sustainability (reduced compute), (b) economic access (lower cost enables smaller organizations to use multi-agent systems), (c) potential displacement of human oversight (automated topology selection removes a decision point where humans currently intervene), and (d) quality risks (routing to cheaper topologies may systematically fail on edge cases affecting specific user populations). None of these are discussed.

- **Reproducibility gaps.** Code is not available (placeholder URL in abstract). DIT annotation protocol is undocumented. Logistic regression hyperparameters are not reported. GPT-4o system prompts are not provided. Temperature settings are not specified. The debate protocol (number of rounds, judge selection criteria) is described at a high level but lacks implementation detail. A reviewer attempting to reproduce this work would need to make many undocumented choices.

- **Cost claims without real billing validation.** The annual savings estimates ($89K in introduction, $58K in discussion) are based on simulated token costs. Production API billing includes many factors not modeled: minimum charge per request, batch vs. real-time pricing tiers, rate-limit-induced retries, context window limits that force request splitting. Presenting these dollar figures without qualification risks misleading practitioners about achievable savings.

- **No analysis of routing failures.** What happens when ParetOrch routes incorrectly? If the quality predictor underestimates success probability for debate and routes a hard task to flat, the task fails. The paper reports aggregate accuracy but does not analyze the distribution of routing errors -- specifically, how often ParetOrch routes to a cheaper topology that fails on a task where a more expensive topology would have succeeded. This "regret" analysis is standard in the routing literature (cf. RouterBench).

- **Synthetic benchmark risks optimistic evaluation.** Tasks designed to span the DIT space may be more separable (in DIT coordinates) than real workloads. Production tasks often have ambiguous DIT profiles -- a coding task that requires both decomposition and iterative debugging might confuse the router. The paper does not test on ambiguous or adversarial cases.

### Questions for Authors

1. Can you provide a routing regret analysis? Specifically: for each task, did ParetOrch select a topology that failed when a more expensive topology would have succeeded? What fraction of errors are "avoidable" through better routing?

2. What is the actual API cost of running the 2,016 experiments? The paper simulates costs but presumably you actually called GPT-4o for the task evaluations. Comparing simulated vs. actual billed costs would validate (or invalidate) the cost model.

3. Will you add a broader impact statement discussing the environmental benefits and potential risks of automated topology selection?

### Recommendation: **Borderline Accept (6)**

The paper is more transparent than average about its limitations, and the contribution has genuine practical value. The missing broader impact statement and reproducibility gaps are addressable in revision. The cost validation concern is more fundamental but acknowledging it explicitly (as the paper does) partially mitigates the issue.

---

## Consensus Assessment

### Aggregate Scores

| Criterion     | R1 (Meth.) | R2 (Nov.) | R3 (Clar.) | R4 (Domain) | R5 (Ethics) | **Mean** |
|---------------|-----------|-----------|-----------|------------|------------|----------|
| Quality       | 5         | 6         | 6         | 5          | 6          | **5.6**  |
| Clarity       | 7         | 7         | 8         | 7          | 7          | **7.2**  |
| Originality   | 6         | 6         | 6         | 7          | 6          | **6.2**  |
| Significance  | 5         | 6         | 7         | 5          | 6          | **5.8**  |
| **Overall**   | 5         | 6         | 6         | 5          | 6          | **5.6**  |

### Score Distribution

- **Strong Reject:** 0
- **Reject:** 0
- **Borderline Reject:** 2 (R1, R4)
- **Borderline Accept:** 3 (R2, R3, R5)
- **Accept:** 0
- **Strong Accept:** 0

### Inter-Reviewer Disagreements

**Quality (spread = 1):** Moderate agreement. R1 and R4 are harder on quality (5) due to methodological concerns, while R2, R3, R5 rate it at 6.

**Significance (spread = 2):** R3 rates significance at 7 (practical value to industry) while R4 rates it 5 (insufficient realism for academic impact). This reflects a genuine tension: the paper is more useful to practitioners than to researchers.

**Originality (spread = 1):** Narrow agreement at 6-7. All reviewers agree the gap identification is genuine but the technical contribution is modest.

### Key Agreement Points (3+ reviewers agree)

1. **Simulated costs are a fundamental limitation** (R1, R4, R5 all flag this)
2. **DIT labeling process is undocumented** (R1, R3, R4 all flag this)
3. **Synthetic task suite limits generalizability claims** (R1, R4, R5 all flag this)
4. **The writing and framing are strong** (R1, R2, R3, R5 all agree)
5. **The crossover insight is the paper's most valuable contribution** (R2, R4 explicitly; R1, R3 implicitly)

---

## Must-Fix Issues

Issues that **must** be resolved before this paper is acceptable at NeurIPS.

### MF-1: Resolve Figure-Text Data Inconsistency (Critical)

**Flagged by:** R3
**Location:** figure_metadata.json vs. main.tex/results.md
**Problem:** The figure metadata references 82 tasks with 2 difficulty tiers (Easy n=12, Hard n=70) and very different accuracy numbers (flat overall=28.9% vs. Table 1 flat=68.8%). The paper text uses 84 tasks with 4 difficulty tiers. These appear to come from different experiment runs. If the figures were generated from a prior experiment and the tables from the final experiment, the figures do not depict the data described in the paper.
**Fix:** Regenerate all figures from the final 84-task, 4-tier dataset (pareto_results.json). Ensure every figure caption references the correct n, tiers, and accuracy values. Verify that each figure's underlying data matches the corresponding table in the text.

### MF-2: Document DIT Annotation Protocol (Critical)

**Flagged by:** R1, R3, R4
**Location:** Section 4.1
**Problem:** The DIT features are the foundation of the entire routing framework. Their origin, reliability, and reproducibility are never specified. The task JSON shows round-number DIT values (0.0, 0.1, 0.2, ...), strongly suggesting manual annotation. The paper does not state who labeled them, whether multiple annotators were used, or what inter-annotator agreement was achieved.
**Fix:** Add a paragraph to Section 4.1 explicitly stating: (a) DIT features were hand-annotated by N annotators, (b) inter-annotator agreement was measured by Cohen's kappa or ICC, (c) disagreements were resolved by [method], (d) the annotation rubric is provided in the supplementary material. If DIT was extracted by a classifier, describe the classifier architecture, training data, and accuracy.

### MF-3: Add Held-Out Evaluation of Quality Predictor (Critical)

**Flagged by:** R1
**Location:** Section 3.3, Section 5
**Problem:** The logistic regression quality predictor is trained and evaluated on the same 84 tasks. No cross-validation, held-out split, or calibration analysis is reported. This makes it impossible to assess whether the predictor generalizes to unseen tasks.
**Fix:** Report leave-one-category-out cross-validation (12 folds across 12 task categories). Report both routing accuracy and calibration (ECE) on the held-out folds. If cross-validated performance is substantially lower than in-sample, discuss implications for deployment.

### MF-4: Strengthen Statistical Analysis (Major)

**Flagged by:** R1
**Location:** Section 5.5
**Problem:** 95% CIs from n=3 seeds (t-distribution with 2 df) provide minimal statistical coverage. The reported CI width (3.8pp for accuracy) appears narrower than expected for n=3, suggesting the CIs may use z-intervals rather than t-intervals.
**Fix:** Either (a) increase to 10+ seeds and report proper bootstrap CIs, or (b) use the correct t_{0.025,2}=4.303 multiplier and honestly report the wide intervals, or (c) clearly caveat that CIs are approximate due to small n. At minimum, report which CI formula was used (z-based, t-based, or bootstrap).

### MF-5: Add Broader Impact Statement (Required)

**Flagged by:** R5
**Location:** Missing (should be after Conclusion)
**Problem:** NeurIPS requires a broader impact statement. The paper has no discussion of environmental implications (reduced compute), economic access implications (lower cost enables smaller organizations), or potential risks (systematic routing failures on underrepresented task types).
**Fix:** Add a Broader Impact section covering positive impacts (reduced compute cost and energy consumption) and potential risks (quality degradation on edge cases, removal of human decision points in agent coordination).

---

## Should-Fix Issues

Issues that would **improve** the paper but are not blocking.

### SF-1: Validate on External Benchmark

**Flagged by:** R4
**Recommendation:** Run ParetOrch on 20-30 GAIA tasks with real multi-agent frameworks. Even a small-scale external validation would significantly address the generalization concern. This could be presented as an appendix experiment.

### SF-2: Add Routing Regret Analysis

**Flagged by:** R5
**Recommendation:** For each task, report whether ParetOrch selected a topology that failed when a more expensive topology would have succeeded. Compute "avoidable failure rate" as the fraction of ParetOrch failures that a more expensive topology would have caught. This is standard in the routing literature (RouterBench).

### SF-3: Compare Simulated vs. Actual API Costs

**Flagged by:** R1, R5
**Recommendation:** If the experiments involved actual GPT-4o API calls, compare the billed costs to the simulated costs. This would validate (or bound the error of) the multiplicative cost model.

### SF-4: Add a Trivial Baseline for DIT Routing

**Flagged by:** R2
**Recommendation:** Test whether a simple proxy (e.g., task description length > 200 tokens -> debate, else flat) captures most of the routing benefit. This would help calibrate how much value the full DIT framework adds over a trivial heuristic.

### SF-5: Report Logistic Regression Coefficients

**Flagged by:** R3
**Recommendation:** The paper argues logistic regression was chosen for interpretability (Section 3.3) but never reports the learned coefficients. Providing w_a vectors and discussing which DIT features have largest weights per topology would strengthen the interpretability claim.

### SF-6: Expand Topology Set

**Flagged by:** R2, R4
**Recommendation:** Adding even one more topology (pipeline, voting, ensemble) would demonstrate that ParetOrch scales beyond 3 options and provide a richer Pareto frontier. This could be a revision experiment.

### SF-7: Reconcile Annual Savings Figures

**Flagged by:** R3
**Recommendation:** The Introduction quotes $89K/year (debate-vs-flat gap) while the Discussion quotes $58K/year (ParetOrch savings vs. debate). Make clear these are different quantities or unify the illustrative calculation.

---

## Verdict

### **CONDITIONAL ACCEPT -- Score: 5.6/10 (below 7.0 threshold)**

The paper identifies a genuine and practically important gap (topology-level cost optimization), provides a clean formulation, and reports a useful empirical insight (DIT crossover at ~0.35). The writing quality is above average. However, five critical issues must be addressed:

1. **Figure-text inconsistency** suggests the figures and tables may derive from different experiments
2. **Undocumented DIT annotation** undermines the foundation of the routing framework
3. **No held-out evaluation** of the quality predictor
4. **Thin statistical coverage** (n=3 seeds) with potentially incorrect CI formula
5. **Missing broader impact statement** (NeurIPS requirement)

**Path to acceptance:** Fix MF-1 through MF-5. If the cross-validated predictor performance remains strong and the figure-text inconsistency is resolved by regenerating figures, the paper's aggregate score would likely rise to 6.0-6.5 with the current reviewer panel. Addressing SF-1 (external benchmark validation) would push it above 7.0.

**Current aggregate: 5.6** (below 7.0 threshold)
**Projected after Must-Fix: 6.0-6.5** (borderline)
**Projected after Must-Fix + SF-1: 7.0-7.5** (accept range)

---

*Review generated by 5-persona NeurIPS council. Each reviewer evaluated the paper independently before aggregation.*
