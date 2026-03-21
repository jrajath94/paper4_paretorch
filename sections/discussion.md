---
section: discussion
word_count: 830
citation_count: 7
draft_version: 1
---

## Discussion

We interpret the experimental findings from Section 5 along four dimensions: deployment implications, the DIT crossover insight, limitations of the current study, and directions for future work.

### 6.1 Practical Implications for Deployment

Most real-world agent workloads are dominated by easy tasks. In production systems, 40--60\% of tasks have DIT magnitude below 0.35, meaning the flat topology handles them at 94\%+ accuracy with no coordination overhead. ParetOrch exploits this distributional property: the router saves 35.5\% of total cost relative to always-debate. For a team processing 10,000 tasks per day at debate-level pricing, this translates to approximately \$187/day or \$68K/year in reduced API spend.

ParetOrch gives practitioners a single knob---the quality threshold $\tau$---to navigate the cost-quality frontier. No understanding of topology internals is required. Lowering $\tau$ to 0.65 pushes savings to 50.0\% at 91.0\% quality retention; raising $\tau$ to 0.85 recovers 97.7\% of peak quality at 8.0\% savings. This operationally simple interface contrasts with model-level routing systems like FrugalGPT \cite{chen2023frugalgpt}, which achieved 98\% cost reduction via model cascading but addressed an orthogonal dimension. FrugalGPT answers "which model?" while ParetOrch answers "which coordination pattern?"---and the two decisions compose. An organization could first cascade models to select the cheapest adequate LLM, then apply ParetOrch to select the cheapest adequate topology, stacking savings along independent axes.

### 6.2 The DIT Crossover Insight

The empirical crossover at DIT magnitude $\sim$0.35 provides a deployable rule of thumb: tasks below this threshold never benefit from multi-agent coordination enough to justify the cost. On trivial tasks ($\|\mathbf{d}\| < 0.35$), flat actually outperforms debate (96.1\% vs. 94.1\%), while debate costs 3.5x more than flat. The gap is not statistically significant ($p = 0.41$). Paying 3.5x for no quality gain is waste, not caution.

This crossover connects to Simon's theory of near-decomposability \cite{simon1962complexity}: tasks with low decomposability, low iterativeness, and low tool diversity are inherently monolithic. They resist multi-agent decomposition because there is nothing to decompose, no iterative refinement to parallelize, and no tool orchestration to coordinate. The multi-agent overhead becomes pure cost with no quality return.

The DIT crossover is model-agnostic in structure, though its location will shift. As base model quality improves, the crossover point moves upward---stronger models make flat topology competitive on harder tasks, expanding the regime where single-agent execution suffices. The three-dimensional DIT characterization persists regardless: decomposability, iterativeness, and tool diversity remain structural task properties independent of model capability. Concurrent work DAAO \cite{su2025daao} corroborates this insight from a different angle. Their difficulty-aware switching at the operator level found that easy operations do not benefit from complex agent coordination, paralleling our topology-level finding. The convergence across independent studies strengthens confidence in difficulty-aware routing as a general principle.

### 6.3 Limitations

We identify six limitations of the current study that scope the generalizability of our findings.

First, costs are simulated from token counting rather than real API billing. Production API costs include overhead from rate limiting, retries on transient failures, and billing granularity (rounding to token buckets). Our cost model captures the dominant component---token volume scaled by per-token price---but may underestimate total deployment cost by 10--20\%.

Second, DIT features require task analysis before routing. This preprocessing step adds latency (under 5ms in our implementation) and assumes that task descriptions contain sufficient signal for DIT extraction. Tasks with ambiguous or incomplete descriptions may receive inaccurate DIT estimates.

Third, we evaluate three topologies only. Real systems may deploy additional patterns: pipelines with sequential handoff, round-robin ensembles, voting mechanisms, or hybrid topologies that mix coordination strategies within a single task. ParetOrch's framework extends to $|\mathcal{A}| > 3$ without modification, but the cost-quality tradeoff surface may change with a richer topology set.

Fourth, the logistic regression quality predictor is deliberately simple. More expressive models (gradient-boosted trees, small neural networks) may improve routing accuracy on edge cases near the crossover boundary, at the cost of calibration guarantees and interpretability.

Fifth, evaluation uses synthetic tasks designed to span the DIT space. Real-world workload distributions may cluster differently---for example, production coding assistants may concentrate tasks in the medium-difficulty range, reducing the proportion of trivial tasks where ParetOrch's savings are largest.

Sixth, all experiments use a fixed base model (GPT-4o). The cost multipliers $\alpha_a$ and quality predictor coefficients $\mathbf{w}_a$ are fit to this model. Different model families (Claude, Gemini, open-weight models) may exhibit different topology-quality relationships, requiring re-calibration.

### 6.4 Future Directions

Four extensions follow naturally from ParetOrch's framework.

**Online adaptation.** The current router uses offline-trained cost models and quality predictors. Deploying contextual bandits \cite{agrawal2013contextual_ts} over streaming execution data would enable the router to update its estimates continuously, adapting to distribution shift in workload difficulty and model behavior without retraining.

**Learned cost models.** Our multiplicative approximation ($c(a,x) = \alpha_a \cdot c_{\text{base}}(x)$) is deliberately simple and misses non-multiplicative factors. Neural cost predictors trained on execution traces could model retry rates, context window overflow, and topology-specific token amplification patterns.

**Topology expansion.** Adding topologies (pipeline, ensemble, voting) to the candidate set $\mathcal{A}$ enlarges the Pareto frontier and may reveal new cost-quality operating points inaccessible with three topologies. The router's feasibility-then-minimize structure scales linearly with $|\mathcal{A}|$.

**Cross-model $\times$ cross-topology routing.** The most impactful extension jointly selects model and topology, combining the cost axis of RouteLLM \cite{ong2024routellm} with the topology axis of ParetOrch. A joint router over $|\text{models}| \times |\text{topologies}|$ configurations could navigate a richer Pareto surface, achieving cost reductions neither system captures alone. Extending the Pareto frontier from two objectives (cost, quality) to three (cost, quality, latency) would further align the optimization with production deployment constraints.
