# ParetOrch: 5-Reviewer Council Review (NeurIPS 2026)

**Paper:** ParetOrch: Cost-Quality Pareto Optimization for Multi-Agent LLM Topology Selection
**Review Round:** Council Final
**Date:** 2026-03-17
**Venue Target:** NeurIPS 2026

---

## Reviewer 1: Methodology Skeptic

**Confidence: 4/5** (Confident -- have published on multi-agent evaluation and cost modeling)

### Scores

| Criterion | Score | Weight |
|-----------|-------|--------|
| Quality | 5 | 40% |
| Clarity | 7 | 15% |
| Originality | 6 | 15% |
| Significance | 6 | 30% |
| **Weighted Overall** | **5.7** | |

### Summary

The paper proposes routing tasks to different multi-agent topologies (flat, hierarchical, debate) based on DIT features, optimizing for cost subject to a quality floor. The experimental design is structured, with seed replication and confidence intervals, which is appreciated. However, several methodological concerns prevent me from recommending acceptance in the current form.

### Strengths

**S1. Controlled experimental design.** Holding the base model constant (GPT-4o) across all topologies isolates the topology effect from the model effect. This is a smart design decision that many multi-agent papers fail to make.

**S2. Seed replication with CIs.** Three-seed replication with 95% confidence intervals reported throughout (e.g., "77.7% +/- 2.1" in Table 1) exceeds the statistical reporting standard of many NeurIPS submissions in this area.

**S3. Ablation study.** Table 3 systematically removes each component (DIT features, cost model, Pareto optimization) and measures the marginal contribution. The "random within threshold" variant is a particularly instructive ablation.

**S4. Falsifiable crossover claim.** The DIT magnitude ~0.35 crossover is a concrete, testable prediction. The paper does not oversell it -- trivial-task significance testing ($p = 0.41$) correctly flags the small gap as noise.

### Weaknesses

**W1 (Critical). 84 tasks is underpowered for the claims made.** The paper evaluates on 84 tasks across 4 difficulty tiers, meaning only 17-24 tasks per tier. The per-tier confidence intervals reflect this: the hard-tier debate accuracy of 61.1% is based on 24 tasks x 3 seeds = 72 runs. For a logistic regression classifier with 7 features, 84 total training/evaluation points is thin. The paper does not report a train/test split for the quality predictor -- is the predictor evaluated on the same data it was trained on? If so, the routing accuracy numbers are inflated.

**Specific fix:** Report the train/test split or cross-validation procedure for the quality predictor. If leave-one-out or k-fold was used, state it explicitly. If the predictor was fit on all 84 tasks and then evaluated on the same 84, this is a fatal methodological flaw that must be addressed. Additionally, discuss statistical power: 17 trivial tasks is barely enough for a permutation test.

**W2 (Critical). Simulated costs, not real API costs.** Section 6.3 acknowledges that "costs are simulated from token counting rather than real API billing" and may "underestimate total deployment cost by 10-20%." For a paper whose primary contribution is cost optimization, this is a significant limitation. Rate limiting, retries, context window overflow, and billing granularity are not theoretical concerns -- they are dominant cost factors in production. The cost model $c(a,x) = \alpha_a \cdot (p_{in} \cdot n_{in}(x) + p_{out} \cdot n_{out}(x))$ is a first-order approximation.

**Specific fix:** (a) Report actual API billing for at least a subset of tasks to validate the token-counting proxy. (b) Quantify the gap between simulated and actual costs. (c) If actual billing is unavailable, at minimum add noise to the cost model (+/- 20%) and show that routing decisions are robust to cost estimation error.

**W3 (Important). Cost model validation is incomplete.** The paper reports that overhead ratios were $2.10 \pm 0.08$ (hierarchical) and $2.80 \pm 0.11$ (debate) with "no statistically significant interaction between overhead ratio and difficulty tier (Kruskal-Wallis $p = 0.34$)." But the multiplicative assumption $c(a,x) = \alpha_a \cdot c_{base}(x)$ also assumes that the overhead ratio is independent of task *type* within a difficulty tier. A coding task and a summarization task at the same difficulty level may have very different coordinator overhead in the hierarchical topology. This interaction is not tested.

**Specific fix:** Report overhead ratios stratified by task category (coding, reasoning, summarization, etc.) in addition to difficulty tier. If the interaction is non-significant, say so with a test. If it is significant, the cost model needs category-conditional overhead multipliers.

**W4 (Important). Missing comparison to oracle routing.** The paper does not report oracle routing -- the cost of a policy that has perfect knowledge of which topology succeeds on which task and selects the cheapest successful topology. Without this upper bound, it is hard to assess how much room remains for improvement over ParetOrch. How close is ParetOrch to optimal?

**Specific fix:** Add oracle routing as a row in Table 1. The oracle achieves the minimum possible cost at 100% accuracy among the three topologies. Report the gap between ParetOrch and oracle to quantify routing error.

**W5 (Minor). Three seeds is minimal.** While three seeds with CIs is better than single-seed reporting, NeurIPS expectations are trending toward 5+ seeds or bootstrap confidence intervals. The CI width of 3.8pp for accuracy suggests meaningful variance. With 5 seeds, CIs would narrow and the statistical credibility of marginal claims (e.g., 2.7pp accuracy gap between ParetOrch and DIT Accuracy-Only) would strengthen.

### Questions for Authors

1. Is the quality predictor trained on the same 84 tasks used for evaluation? If so, what prevents overfitting given the low n:feature ratio (84:7)?
2. What is the oracle routing cost? How far is ParetOrch from the optimum?
3. Have you measured actual API billing for any task subset to validate the simulated cost model?

### Overall Assessment

The experimental framework is well-structured but underpowered. The 84-task evaluation, potential train-test contamination, and simulated (not real) costs collectively weaken the empirical claims. The paper would benefit from (a) a clear train/test split description, (b) cost model validation against real billing, and (c) oracle routing comparison. Currently a **borderline reject** -- the ideas are sound but the evidence is not strong enough for NeurIPS.

**Overall Recommendation: 5 (Borderline Reject)**

---

## Reviewer 2: Novelty Assessor

**Confidence: 3/5** (Fairly confident -- familiar with LLM routing and multi-agent systems but not a core contributor)

### Scores

| Criterion | Score | Weight |
|-----------|-------|--------|
| Quality | 6 | 15% |
| Clarity | 7 | 15% |
| Originality | 6 | 40% |
| Significance | 6 | 30% |
| **Weighted Overall** | **6.1** | |

### Summary

The paper positions itself in the gap between model-level routing (FrugalGPT, RouteLLM) and topology-level routing. The framing is clean and the gap is real -- no prior work explicitly routes between orchestration topologies based on cost-quality Pareto optimization. However, the technical novelty is moderate: the actual routing mechanism is a logistic regression + argmin over three options, and the DIT features are inherited from prior work.

### Strengths

**S1. Clean problem formulation.** The paper clearly distinguishes model-level routing from topology-level routing. The constrained optimization (minimize cost subject to quality floor) is precisely stated and the connection to contextual bandits is appropriate.

**S2. Genuine gap in literature.** The related work section is thorough (22 citations) and convincingly argues that no prior work jointly optimizes cost and quality at the topology level. The three-thread organization (cost-aware routing, multi-agent topologies, Pareto optimization) with an explicit "gap" subsection is effective.

**S3. Actionable crossover insight.** The DIT magnitude ~0.35 crossover is the paper's most original contribution. It provides a deployable heuristic: "if DIT < 0.35, use flat." This is concrete, testable, and immediately useful to practitioners. It also connects nicely to Simon's near-decomposability theory.

**S4. Composability argument.** The observation that topology-level routing is orthogonal to model-level routing and can be stacked (Section 6.1) is insightful. This positions ParetOrch as complementary to rather than competing with FrugalGPT/RouteLLM.

### Weaknesses

**W1 (Important). Limited technical novelty in the routing mechanism.** The core algorithm is: (1) compute 7 features from DIT, (2) run 3 logistic regressions, (3) filter by threshold, (4) pick the cheapest. This is a standard feasibility-then-minimize approach applied to a new domain. The method section (Section 3) is well-written but the actual algorithmic contribution is modest. There is no new optimization algorithm, no new learning procedure, no theoretical guarantee beyond the trivial Pareto dominance argument in Section 3.4.

**Specific fix:** Strengthen the novelty claim in one of two ways: (a) Provide a theoretical result -- e.g., a bound on routing regret relative to oracle, or conditions under which the DIT crossover is provably correct. (b) Demonstrate that the seemingly simple approach outperforms more complex alternatives (e.g., show that replacing logistic regression with a neural router does not significantly improve results, validating the sufficiency of the simple approach as a finding in itself).

**W2 (Important). DIT features are inherited, not introduced.** The paper states that DIT features are from "prior work on task characterization for agent routing." The DIT framework is presented as given rather than developed here. If DIT is the key contribution enabling the routing, and DIT was developed elsewhere, then ParetOrch's novelty rests primarily on applying DIT to a cost-optimization setting. This is more of an application contribution than a methodological one.

**Specific fix:** Be precise about what is new vs. inherited. If DIT was introduced by the same authors in prior work, the self-citation rules apply. If DIT features are refined or extended here (e.g., the pairwise interaction features $D \cdot I, D \cdot T, I \cdot T$ are new), highlight that explicitly. Currently the provenance of DIT is ambiguous.

**W3 (Important). Three topologies may be too narrow.** The paper considers flat, hierarchical, and debate. Real-world systems use many more patterns: sequential pipelines, voting ensembles, tree-of-thought, reflection loops, tool-augmented chains. With only 3 topologies, the routing problem is trivial in some sense -- the argmin is over a set of size 3. The Pareto frontier with 3 points is not particularly rich. The claim of "Pareto optimization" may be overstating what is essentially a 3-way argmin.

**Specific fix:** (a) Discuss how the approach scales to more topologies. (b) Show results with at least one additional topology (e.g., pipeline or ensemble) to demonstrate the framework's extensibility. (c) Acknowledge that with $|\mathcal{A}| = 3$, the "Pareto frontier" is at most 3 points, which limits the optimization landscape.

**W4 (Minor). Overclaiming "first."** The introduction claims "No prior work routes between orchestration topologies based on cost-quality tradeoffs." This negative claim is hard to verify. DAAO (cited in related work) does difficulty-aware agent orchestration with cost reduction (64% of inference cost). While the paper distinguishes DAAO as "operator level" rather than "topology level," the line between these is blurry. A reviewer familiar with DAAO might see this distinction as semantic rather than substantive.

**Specific fix:** Soften the "first" claim or sharpen the distinction. Provide a concrete example where DAAO's operator-level routing would make a different decision than ParetOrch's topology-level routing, demonstrating the gap is functional, not just definitional.

### Questions for Authors

1. What is the provenance of DIT features? Are they from your own prior work or from an independent source?
2. Can you provide a concrete example where operator-level routing (DAAO) and topology-level routing (ParetOrch) lead to different decisions on the same task?
3. Would adding a 4th topology (e.g., sequential pipeline) meaningfully change the Pareto frontier?

### Overall Assessment

The problem framing is the paper's strongest contribution -- the model-vs-topology distinction is genuine and the cost-quality Pareto formulation is clean. The crossover insight is useful and actionable. However, the technical machinery is straightforward (logistic regression + argmin), the DIT features are inherited, and the experimental scope (3 topologies, 84 tasks) limits the generality of the claims. This is a solid workshop paper or a borderline conference paper. For NeurIPS, I would need either stronger theoretical contributions or a more expansive empirical evaluation.

**Overall Recommendation: 6 (Borderline Accept)**

---

## Reviewer 3: Clarity Reviewer

**Confidence: 4/5** (Confident -- extensive experience reviewing NeurIPS/ICML papers for writing quality)

### Scores

| Criterion | Score | Weight |
|-----------|-------|--------|
| Quality | 6 | 15% |
| Clarity | 7 | 40% |
| Originality | 6 | 15% |
| Significance | 6 | 30% |
| **Weighted Overall** | **6.4** | |

### Summary

The paper is well-organized and clearly written for a first draft. The structure is logical, notation is mostly consistent, and the tables are informative. Several areas need tightening for NeurIPS submission quality.

### Strengths

**S1. Clean paper architecture.** The paper follows a standard and effective structure: Introduction (problem + contributions), Related Work (three threads + gap), Method (formalization + algorithm), Experiments (setup), Results (main + stratified + Pareto + ablation + stats), Discussion (implications + limitations + future), Conclusion. Each section has a clear purpose and transitions are smooth.

**S2. Strong quantitative claims in abstract and introduction.** The abstract delivers five concrete numbers (53.4% cost reduction, 95.6% quality retention, 19.3% DIT ablation margin, 0.35 crossover, 2,016 runs). The introduction extends these with difficulty-stratified examples ($p = 0.41$ for trivial, 25.5pp gap for hard). This specificity builds credibility.

**S3. Tables are well-formatted.** Tables 1-3 use clear headers, consistent formatting, and bold for the proposed method. The difficulty-stratified table (Section 5.2) includes both per-topology accuracy and ParetOrch's routing distribution, which is maximally informative.

**S4. Algorithm box is clear.** Algorithm 1 is self-contained, with each step annotated. A reader could implement ParetOrch from this pseudocode alone.

**S5. Limitations section is honest.** Six limitations are enumerated in Section 6.3, including simulated costs, three-topology scope, and fixed base model. This is the right level of intellectual honesty for NeurIPS.

### Weaknesses

**W1 (Important). Missing Methods section figure.** The method section references "Figure 2" for topology cost models but the figure is a placeholder: `[FIGURE: Topology Cost Models by Difficulty Tier | paper/experiments/pareto_results.json:topology_cost_models | grouped_bar_chart]`. The introduction also has a placeholder for "Figure 1" (framework overview). Missing figures are a dealbreaker for review -- no reviewer will evaluate a paper with placeholder figures.

**Specific fix:** Generate both figures before submission. Figure 1 should be a clean system diagram showing: task input -> DIT extraction -> quality prediction -> Pareto selection -> topology output. Figure 2 should be a grouped bar chart with difficulty tiers on x-axis, cost in USD on y-axis, grouped by topology. Figure 3 (Pareto frontier, referenced in Section 5.3) also appears to be missing.

**W2 (Important). Five contributions is too many.** The introduction lists five contributions. NeurIPS reviewers typically expect 2-3 crisp contributions. The current five include redundancy: "Topology-level Pareto optimization" and "Pareto frontier analysis" overlap; "DIT-conditioned routing" and "Actionable crossover insight" overlap. Listing five dilutes the core message.

**Specific fix:** Consolidate to three contributions: (1) Topology-level Pareto routing framework (covering contributions 1 and 4), (2) DIT-conditioned quality prediction with crossover insight (covering contributions 2 and 5), (3) Empirical evaluation at scale (covering contribution 3). Three strong contributions > five diffuse ones.

**W3 (Important). Self-assessment metadata leaks into paper text.** The related work section includes a "Self-Assessment" block, "Known Weaknesses," and "Revision Notes" starting at line 40. The experiments section has similar metadata starting at line 60. These internal review artifacts must be removed before submission. No NeurIPS reviewer should see self-assessment scores or revision notes in the paper body.

**Specific fix:** Strip all metadata blocks (Self-Assessment, Known Weaknesses, Revision Notes) from all section files before compilation into LaTeX. These belong in internal review documents, not in the submitted paper.

**W4 (Minor). Notation inconsistency: $\mathbf{d}$ vs DIT.** The paper alternates between "$\mathbf{d}(x)$" (formal notation), "DIT features" (shorthand), and "DIT magnitude $\|\mathbf{d}\|$" (hybrid). In the introduction, "DIT features" appears before the notation is defined. In the method section, $\mathbf{d}(x) = (D, I, T)$ is formally defined. The abstract uses "DIT" without defining it as an acronym.

**Specific fix:** Define DIT as an acronym in the abstract or introduction (first use should be "Decomposability, Iterativeness, Tool Diversity (DIT)"). Then use $\mathbf{d}$ for the formal vector and "DIT" for the informal concept consistently throughout.

**W5 (Minor). Related work section slightly long.** At 1042 words, the related work is about 20% of the 9-page limit. The Pareto optimization subsection (Section 2.3) is the weakest -- the Marinov-Zimmert and Agrawal-Goyal references feel included for scholarly completeness rather than because they directly inform the method.

**Specific fix:** Trim the Pareto subsection by 30% -- cut references that do not directly motivate a design choice in ParetOrch. Redirect the saved space to the method section, which could benefit from a worked example.

**W6 (Minor). No broader impact statement.** NeurIPS requires a broader impact statement. The current draft has none.

**Specific fix:** Add a 1-paragraph broader impact statement discussing: (a) positive impact of cost reduction (democratizing multi-agent AI for smaller organizations), (b) potential negative impact (cost optimization may incentivize quality shortcuts), (c) environmental benefit of reduced API calls.

### Questions for Authors

1. Will the three placeholder figures be generated before submission?
2. Is there a reason the five contributions cannot be consolidated to three?
3. Will the self-assessment metadata be stripped from the final LaTeX compilation?

### Overall Assessment

The writing is above average for a first draft. The logical structure is sound, tables are informative, and the algorithm presentation is clean. However, three issues need resolution before submission: (1) placeholder figures must be replaced with real visualizations, (2) self-assessment metadata must be stripped, and (3) the contribution list should be consolidated. These are all fixable issues. The underlying writing quality is good.

**Overall Recommendation: 6 (Borderline Accept)**

---

## Reviewer 4: Domain Expert (Multi-Agent Systems)

**Confidence: 5/5** (Very confident -- leading researcher in multi-agent LLM orchestration)

### Scores

| Criterion | Score | Weight |
|-----------|-------|--------|
| Quality | 6 | 25% |
| Clarity | 7 | 15% |
| Originality | 7 | 25% |
| Significance | 7 | 35% |
| **Weighted Overall** | **6.8** | |

### Summary

This paper addresses a real and growing pain point in the multi-agent LLM community: topology selection is currently ad hoc. As someone who has deployed MetaGPT, AutoGen, and debate systems in production, I can confirm that the cost-quality tradeoff described here is genuine. The $245/day gap between always-flat and always-debate (Section 1) is conservative -- in practice, the gap is larger due to retries, context window overflow, and rate limit delays. The DIT crossover insight aligns with my operational experience: simple tasks do not benefit from multi-agent overhead.

### Strengths

**S1. Solves a real problem practitioners face.** Every team deploying multi-agent systems makes the flat-vs-hierarchical-vs-debate decision. Currently this is done by vibes, not data. ParetOrch provides a principled, single-parameter framework for this decision. This is immediately deployable.

**S2. The crossover insight is the real contribution.** More than the routing algorithm itself, the finding that DIT < 0.35 means "always use flat" is a high-impact result. It gives practitioners a free lunch: reduce costs immediately by routing trivial tasks to single-agent execution. I would cite this paper for this insight alone.

**S3. Composability with model routing is correctly identified.** The observation that topology routing and model routing are orthogonal (Section 6.1) is important. In production, you want to select both the cheapest adequate model AND the cheapest adequate topology. The paper correctly frames these as independent axes.

**S4. Honest about scope.** The limitations section identifies six concrete scoping issues. The fixed-model limitation (GPT-4o only) and three-topology limitation are stated without hedging. This honesty is appreciated.

**S5. Cost per correct answer is the right metric.** Most routing papers report total cost and accuracy separately. Cost per correct answer ($0.023 vs $0.047) is the metric that matters for deployment: it captures efficiency per unit of useful work. This metric choice shows practitioner awareness.

### Weaknesses

**W1 (Important). No real-world deployment validation.** The evaluation uses 84 synthetic tasks with DIT annotations. A domain expert wants to see this on real workloads: production coding assistant logs, customer service agent traces, or research workflow recordings. Synthetic tasks may not capture the distributional properties of real workloads (e.g., the 43% flat-routing finding is from this limited synthetic task set and may not generalize to real deployments).

**Specific fix:** Add even a small real-world case study. Take 50-100 tasks from a real agent deployment, annotate them with DIT, and show that ParetOrch's routing decisions match what an expert would choose. Alternatively, use publicly available benchmarks like GAIA (already cited) or SWE-bench.

**W2 (Important). DIT annotation scalability is unaddressed.** The paper assumes DIT features are "available before routing" and can be extracted by "a small classifier trained on task descriptions." But this classifier is not described, not trained, not evaluated. In production, DIT extraction IS the bottleneck -- getting accurate decomposability and iterativeness scores from a task description is itself a non-trivial ML problem. The paper hand-waves over the hardest part of the deployment pipeline.

**Specific fix:** Train a DIT classifier on the 84 annotated tasks and evaluate its accuracy (even if leave-one-out). Report the downstream impact of DIT estimation errors on routing quality. If a 10% error in DIT magnitude shifts the routing decision for tasks near the 0.35 crossover, that matters.

**W3 (Important). No latency analysis.** Cost is one dimension, but latency is equally important for practitioners. Debate topology takes much longer than flat -- not just in tokens but in wall-clock time (sequential rounds of critique). A production system often cannot afford debate latency even if the cost is acceptable. The paper optimizes cost but ignores latency entirely.

**Specific fix:** Report wall-clock latency per topology per difficulty tier. If latency data is available, add it as a third objective. If not, at minimum discuss how latency constraints would modify the routing decisions.

**W4 (Minor). The $\tau$ parameter requires tuning.** The paper presents $\tau$ as "a single knob" but does not discuss how a practitioner should set it. The Pareto frontier table (Section 5.3) shows the tradeoff, but in practice, setting $\tau$ requires understanding the quality predictor's calibration. If the predictor is poorly calibrated, $\tau = 0.75$ might deliver 70% or 80% actual quality. How sensitive are outcomes to $\tau$ miscalibration?

**Specific fix:** Report calibration curves (predicted probability vs actual success rate) for the quality predictor. Show that $\tau = 0.75$ actually delivers ~75% quality, not some other number.

### Questions for Authors

1. Have you tested ParetOrch on any real agent deployment workload?
2. What is the accuracy of automated DIT extraction, and how do estimation errors propagate to routing quality?
3. What are the wall-clock latency differences between topologies?

### Overall Assessment

This paper addresses a genuine need in the multi-agent community. The framing is strong, the crossover insight is valuable, and the composability argument is forward-looking. However, the gap between this paper and real-world deployment is wider than it appears: DIT annotation is assumed rather than solved, real-world workloads are absent, and latency is ignored. I recommend **borderline accept** with encouragement to add deployment validation in the camera-ready. The core idea is sound and practitioners will find value here.

**Overall Recommendation: 7 (Accept)**

---

## Reviewer 5: Ethics & Reproducibility Reviewer

**Confidence: 4/5** (Confident -- specialize in reproducibility standards and research ethics)

### Scores

| Criterion | Score | Weight |
|-----------|-------|--------|
| Quality | 6 | 20% |
| Clarity | 7 | 20% |
| Originality | 6 | 20% |
| Significance | 6 | 20% |
| Reproducibility | 5 | 20% |
| **Weighted Overall** | **6.0** | |

### Summary

I evaluate this paper on reproducibility, ethical considerations, and fairness of evaluation. The paper shows awareness of good experimental practice (seed replication, CIs) but falls short on several reproducibility requirements.

### Strengths

**S1. Seed replication and CI reporting.** Three seeds (42, 137, 2024) with 95% confidence intervals is the minimum expected standard, and the paper meets it. The per-seed breakdown in Section 5.5 (seed 42: 53.6%/93.5%, seed 137: 53.1%/98.2%, seed 2024: 53.4%/95.1%) adds transparency.

**S2. Statistical significance testing.** The paper uses permutation tests and reports p-values where claims depend on statistical significance ($p = 0.41$ for trivial, $p < 0.01$ for medium). It correctly refrains from claiming significance where it does not exist.

**S3. Code and data promise.** The abstract states "Code and data are available at [repository URL]." If delivered, this is a strong reproducibility commitment.

**S4. Algorithm pseudocode is complete.** Algorithm 1 is detailed enough to reimplement from scratch. The feature expansion, logistic regression formulation, and selection rule are all specified.

### Weaknesses

**W1 (Critical). Repository URL is a placeholder.** The abstract states "Code and data are available at [repository URL]" with no actual URL. For NeurIPS submission, an anonymous supplementary repository is expected. The promise of code without actual code is not reproducibility -- it is a promissory note. NeurIPS reviewers increasingly check linked repositories.

**Specific fix:** Create an anonymous repository (e.g., anonymous.4open.science) containing: (a) the 84 task descriptions with DIT annotations, (b) all 2,016 run outputs, (c) the ParetOrch routing code, (d) logistic regression training code with hyperparameters, (e) scripts to reproduce all tables and figures. Include a README with exact reproduction instructions.

**W2 (Critical). DIT annotation process is opaque.** The 84 tasks each have DIT annotations on [0,1] for three dimensions. The paper does not describe: (a) who annotated the tasks (authors? crowdworkers? LLM?), (b) the annotation guidelines, (c) inter-annotator agreement, (d) whether annotations were validated. If the DIT labels are subjective human judgments, reproducibility requires the annotation protocol. If they are extracted by a classifier, the classifier must be described and released.

**Specific fix:** Describe the DIT annotation process in detail. If human-annotated: report the number of annotators, the guidelines they followed, and Cohen's kappa or Krippendorff's alpha for inter-annotator agreement. If machine-annotated: describe the extraction model and its validation. Include the full annotation data in the supplementary material.

**W3 (Important). Task suite provenance unclear.** How were the 84 tasks created or selected? Were they hand-crafted by the authors? Sampled from an existing benchmark? Generated by an LLM? The task selection methodology affects the validity of all downstream results. If tasks were designed to demonstrate the DIT crossover, the results are tautological.

**Specific fix:** Describe the task creation/selection process. If tasks are original, describe the design criteria. If sourced from existing benchmarks, cite them. If generated, describe the generation process and quality control. Release all 84 task descriptions in supplementary material.

**W4 (Important). Quality predictor training details missing.** The logistic regression quality predictor has 7 features and 3 topology-specific coefficient vectors. The paper does not report: (a) regularization (L1? L2? strength?), (b) training procedure, (c) convergence criteria, (d) calibration assessment, (e) train/test split. These are standard reproducibility requirements for any ML model.

**Specific fix:** Add a "Training Details" paragraph in Section 3.3 or Section 4 specifying: regularization type and strength, optimization method, convergence criterion, and most importantly, the evaluation protocol (cross-validation, held-out test set, etc.). Report calibration metrics (Brier score, ECE).

**W5 (Important). GPT-4o version pinning.** The paper specifies "GPT-4o (2024-08-06)" which is good. However, OpenAI API behavior can change even for pinned model versions (system prompt changes, safety filter updates). Were all 2,016 runs conducted in a single time window, or spread across weeks/months? API drift between runs would compromise reproducibility.

**Specific fix:** Report the date range of experimental runs. If runs were spread over time, report whether per-model behavior changed. Include API response metadata (e.g., token counts, finish reasons) in supplementary data.

**W6 (Minor). No broader impact or ethics statement.** NeurIPS requires an ethics statement. The paper has none. While the work itself raises no immediate ethical concerns, cost optimization in AI systems has implications: (a) encouraging volume-over-quality reasoning, (b) environmental impact of reduced but still substantial API usage, (c) cost savings may not pass through to end users.

**Specific fix:** Add a broader impact statement. Discuss positive impact (cost democratization), potential negative impact (quality shortcuts), and environmental considerations.

**W7 (Minor). No NeurIPS reproducibility checklist.** NeurIPS requires a completed reproducibility checklist as part of the submission. This is absent.

**Specific fix:** Complete the NeurIPS reproducibility checklist. For each item, either confirm compliance or explain why the item does not apply.

### Questions for Authors

1. Will the anonymous repository be available before the review deadline?
2. Who annotated the DIT features, and what was the inter-annotator agreement?
3. Were all 2,016 runs conducted in a single time window?

### Overall Assessment

The paper demonstrates awareness of statistical best practices (seed replication, CIs, significance testing) but falls short on core reproducibility requirements: the repository is a placeholder, the DIT annotation process is opaque, task provenance is unclear, and training details are missing. These are fixable issues, but they must be fixed BEFORE submission, not after. A NeurIPS reviewer who checks the repository link and finds a placeholder will downgrade the paper significantly.

**Overall Recommendation: 5 (Borderline Reject)**

---

# Aggregate Assessment

## Score Summary

| Criterion | R1 (Methodology) | R2 (Novelty) | R3 (Clarity) | R4 (Domain) | R5 (Ethics/Repro) | **Mean** |
|-----------|:-:|:-:|:-:|:-:|:-:|:-:|
| Quality | 5 | 6 | 6 | 6 | 6 | **5.8** |
| Clarity | 7 | 7 | 7 | 7 | 7 | **7.0** |
| Originality | 6 | 6 | 6 | 7 | 6 | **6.2** |
| Significance | 6 | 6 | 6 | 7 | 6 | **6.2** |
| **Overall** | **5** | **6** | **6** | **7** | **5** | **5.8** |
| Confidence | 4 | 3 | 4 | 5 | 4 | 4.0 |

## Inter-Reviewer Agreement

**Consensus: Majority (3/5 at borderline or above; 2/5 at borderline reject)**

- R1 and R5 score overall 5 (borderline reject) on methodological and reproducibility grounds
- R2 and R3 score overall 6 (borderline accept) on novelty and clarity grounds
- R4 scores overall 7 (accept) on domain significance grounds
- **Score spread on Quality: 5-6 (max gap 1)** -- all reviewers agree quality needs work
- **Score spread on Originality: 6-7 (max gap 1)** -- modest agreement the novelty is real but moderate
- **No fatal flaw flagged** but W1 from R1 (potential train-test contamination) is close to fatal if confirmed

## Acceptance Threshold Check

| Criterion | Threshold | Actual | Met? |
|-----------|-----------|--------|------|
| All reviewers >= 6 | Required (or 2x7+ and 1x5) | 5, 6, 6, 7, 5 | **Partial** -- 2 at accept/borderline-accept, 1 at accept, but 2 at borderline-reject |
| Aggregate quality >= 6.0 | Required | 5.8 | **NO** |
| Aggregate clarity >= 6.0 | Required | 7.0 | YES |
| No fatal flaw | Required | None confirmed | YES (pending train/test clarification) |

**Threshold Status: NOT MET** (aggregate quality 5.8 < 6.0; two reviewers below borderline accept)

## Top Issues by Priority

### Critical (Must fix before submission)

1. **Train/test split for quality predictor** (R1-W1): Clarify whether the logistic regression is evaluated on training data. If yes, implement proper cross-validation and re-report routing accuracy.

2. **Repository with code and data** (R5-W1): Create anonymous repository with full reproduction materials. A placeholder URL is not acceptable.

3. **DIT annotation protocol** (R5-W2): Document who annotated, how, and with what agreement metrics.

4. **Placeholder figures** (R3-W1): Generate Figures 1, 2, and 3. No placeholders in submission.

### Important (Strongly expected by reviewers)

5. **Cost model validation** (R1-W2): Validate simulated costs against real API billing, or at minimum conduct robustness analysis with noisy costs.

6. **Oracle routing comparison** (R1-W4): Add oracle as upper bound in Table 1.

7. **Consolidate contributions to 3** (R3-W2): Merge overlapping contributions.

8. **Real-world workload validation** (R4-W1): Even a small case study on real tasks would strengthen the paper.

9. **DIT extraction automation** (R4-W2): Train and evaluate a DIT classifier; report downstream routing impact of estimation errors.

10. **Strip self-assessment metadata** (R3-W3): Remove all internal review artifacts from section files.

11. **Quality predictor training details** (R5-W4): Report regularization, calibration metrics, evaluation protocol.

12. **Broader impact statement** (R3-W6, R5-W6): Required by NeurIPS.

### Minor (Would strengthen paper)

13. **Latency analysis** (R4-W3): Report wall-clock time per topology.
14. **Cost model stratified by task type** (R1-W3): Test overhead ratio independence from task category.
15. **Sharpen DAAO distinction** (R2-W4): Concrete example where operator-level != topology-level routing.
16. **5+ seeds** (R1-W5): Would narrow CIs and strengthen marginal claims.
17. **NeurIPS reproducibility checklist** (R5-W7): Complete and include.

---

## Verdict

**BORDERLINE REJECT -- revise and resubmit.**

The paper has a genuine contribution: the problem formulation (topology-level vs model-level routing) is clean, the crossover insight is actionable, and the composability argument is forward-looking. The writing is clear and the experimental design has good bones (seed replication, CIs, ablations).

However, the current draft has too many gaps for NeurIPS acceptance:
- The quality predictor may be evaluated on its training data (potentially fatal)
- Costs are simulated without validation
- The repository is a placeholder
- DIT annotation provenance is undocumented
- Figures are missing
- No broader impact statement

None of these are fundamental flaws -- all are fixable with 1-2 weeks of targeted work. The paper's ideas are strong enough to warrant revision rather than abandonment. Address the 4 critical issues and the top important issues, and this paper moves from borderline reject to borderline accept or accept territory.

**Recommended path forward:** Fix critical issues 1-4, then address important issues 5-12. Re-run the council review after revisions.
