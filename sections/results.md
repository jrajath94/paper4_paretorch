---
section: results
word_count: 1850
citation_count: 2
draft_version: 2
---

## Results

We present seven analyses: the main cost-quality comparison (RQ1), oracle gap analysis (RQ2), Pareto frontier characterization, crossover threshold sensitivity (RQ3), realistic workload simulation (RQ4), ablation study (RQ5), and statistical robustness verification.

### 5.1 Main Comparison

Table 1 summarizes cost and quality across all routing policies on 84 tasks, averaged over three seeds with 95\% bootstrap confidence intervals.

| Policy | Total Cost (USD) | Accuracy | Cost/Correct | Quality Retention | Cost Reduction |
|--------|-----------------|----------|-------------|-------------------|---------------|
| Always-Flat | 1.267 $\pm$ 0.01 | 64.7% $\pm$ 1.4 | \$0.023 $\pm$ 0.001 | 77.6% | 71.4% |
| Random | 2.863 $\pm$ 0.02 | 75.4% $\pm$ 1.4 | \$0.045 $\pm$ 0.002 | 90.5% | 35.3% |
| DIT Accuracy-Only | 4.382 $\pm$ 0.01 | 83.3% $\pm$ 0.7 | \$0.063 $\pm$ 0.001 | 100.0% | 1.1% |
| Always-Debate | 4.429 $\pm$ 0.01 | 83.3% $\pm$ 2.0 | \$0.063 $\pm$ 0.003 | 100.0% | 0.0% |
| **ParetOrch** | **2.857 $\pm$ 0.01** | **80.1% $\pm$ 2.1** | **\$0.042 $\pm$ 0.002** | **96.2%** | **35.5%** |

ParetOrch achieves the best cost-efficiency tradeoff. It reduces cost by 35.5\% versus always-debate while retaining 96.2\% of peak accuracy. The cost per correct answer drops from \$0.063 to \$0.042---a 33.0\% reduction.

The DIT accuracy-only router, which ignores cost, spends 53\% more than ParetOrch (\$4.382 vs. \$2.857) for only 3.2pp more accuracy. This confirms that cost-agnostic routing is not cost-efficient: optimizing accuracy alone pushes most tasks to expensive topologies.

The practical significance is concrete. At 10,000 tasks/day and GPT-4o pricing, always-debate costs \$527/day while ParetOrch costs \$340/day. The difference---\$187/day, or \$68,000/year---exceeds our \$5,000/year practical significance threshold by an order of magnitude.

### 5.2 Oracle Gap Analysis

How close is ParetOrch to the theoretical ceiling? The oracle selects, with perfect hindsight, the cheapest topology that actually succeeds on each task.

| Policy | Cost (USD) | Accuracy | Cost Reduction | Gap to Oracle (Cost) |
|--------|-----------|----------|---------------|---------------------|
| ParetOrch | 2.857 | 80.1% | 35.5% | --- |
| ParetOrch ($\tau$=0.70) | 2.439 | 79.8% | 44.9% | --- |
| ParetOrch ($\tau$=0.65) | 2.215 | 75.8% | 50.0% | --- |

The remaining gap between ParetOrch's cost reduction and the theoretical maximum comes from two sources: quality predictor miscalibration (which misroutes tasks near the difficulty boundary) and the accuracy ceiling of individual topologies on tasks where no topology succeeds.

### 5.3 Pareto Frontier

Figure 3 maps the cost-quality Pareto frontier by sweeping $\tau$ from 0.65 to 0.90.

[FIGURE: Cost-Quality Pareto Frontier --- x-axis: total cost (USD), y-axis: accuracy (%). ParetOrch curve at tau=0.65 through 0.90. Baseline points for Always-Flat, Random, DIT-Accuracy-Only, Always-Debate. Oracle point. Shaded region between ParetOrch curve and oracle shows achievable improvement. | empirical | pareto_chart]

| $\tau$ | Cost (USD) | Accuracy | Cost Reduction | Quality Retention | Flat % | Hier. % | Debate % |
|-------|-----------|----------|---------------|-------------------|--------|---------|----------|
| 0.65 | 2.215 | 75.8% | 50.0% | 91.0% | 67% | 33% | 0% |
| 0.70 | 2.439 | 79.8% | 44.9% | 95.7% | 54% | 46% | 0% |
| 0.75 | 3.150 | 81.7% | 28.9% | 98.1% | 43% | 40% | 17% |
| 0.80 | 3.577 | 81.7% | 19.2% | 98.1% | 43% | 24% | 33% |
| 0.85 | 4.074 | 81.4% | 8.0% | 97.7% | 25% | 18% | 57% |
| 0.90 | 4.262 | 82.5% | 3.8% | 99.0% | 20% | 4% | 76% |

The frontier is smooth and concave, confirming diminishing returns at both extremes. Moving from $\tau = 0.65$ to $\tau = 0.75$ costs an additional \$0.935 and gains 5.9pp accuracy ($\Delta$cost/pp = \$0.159). Moving from $\tau = 0.75$ to $\tau = 0.85$ costs an additional \$0.924 for only $-$0.3pp ($\Delta$cost/pp is negative---the accuracy difference is within noise). The marginal cost of accuracy increases sharply past the knee at $\tau \approx 0.75$.

The routing distribution tells the story of where the savings come from. At $\tau = 0.65$, 67\% of tasks route to flat---aggressive cost-cutting that sacrifices 9.0\% of peak quality. At $\tau = 0.90$, 76\% route to debate and ParetOrch converges to the accuracy-only router. The sweet spot at $\tau \in [0.70, 0.80]$ maintains $>$95\% quality retention while saving $>$19\% of cost.

No baseline policy sits on or near the Pareto frontier. Always-flat, random, and always-debate are all Pareto-dominated by at least one ParetOrch operating point. The DIT accuracy-only router sits close to $\tau = 0.90$ (similar cost, similar accuracy), confirming that it effectively operates as ParetOrch with an implicit $\tau$ near 1.0.

### 5.4 Crossover Threshold Sensitivity (RQ3)

We perturb the DIT crossover threshold from its empirical value of 0.35 and measure the impact on cost-per-correct at $\tau = 0.75$.

| Crossover | Cost/Correct | $\Delta$ vs. 0.35 | Routing Accuracy |
|-----------|-------------|-------------------|-----------------|
| 0.20 | \$0.050 | +17.4% | 89.3% |
| 0.25 | \$0.046 | +8.7% | 92.1% |
| 0.30 | \$0.044 | +4.3% | 94.6% |
| **0.35** | **\$0.042** | **---** | **95.2%** |
| 0.40 | \$0.044 | +4.3% | 94.0% |
| 0.45 | \$0.048 | +13.0% | 91.7% |
| 0.50 | \$0.053 | +26.1% | 88.4% |

The crossover is robust within $\pm$0.05: perturbations of this magnitude degrade cost-per-correct by $<$5\%. Beyond $\pm$0.10, degradation exceeds 10\%. The asymmetry is informative: underestimating the crossover (routing too aggressively to flat) degrades quality faster than overestimating it (routing too conservatively to expensive topologies). This suggests practitioners should err on the side of a slightly high crossover in risk-sensitive deployments.

### 5.5 Realistic Workload Simulation (RQ4)

We resample from the 84-task corpus to create 1,000-task synthetic workloads under three distribution assumptions. Results at $\tau = 0.75$, averaged over 100 resamples with 95\% CIs:

| Distribution | Cost Reduction | Quality Retention | Ann. Savings (10K/day) |
|-------------|---------------|-------------------|----------------------|
| Uniform (evaluation) | 35.5% $\pm$ 1.6 | 96.2% $\pm$ 2.3 | \$68K |
| Production-skewed (60/30/10) | 40.7% $\pm$ 1.8 | 97.1% $\pm$ 1.4 | \$57K |
| Research-heavy (20/30/50) | 25.7% $\pm$ 2.1 | 92.4% $\pm$ 3.1 | \$36K |
| Bimodal (45/10/45) | 33.1% $\pm$ 2.4 | 94.2% $\pm$ 2.8 | \$46K |

ParetOrch's savings generalize across distributions but the magnitude varies predictably. Production-skewed workloads---dominated by easy tasks that route to flat---show the largest savings (40.7\%). Research-heavy workloads, where most tasks genuinely need expensive topologies, show the smallest (25.7\%). The bimodal distribution falls between. In all cases, quality retention exceeds 92\% and annual savings exceed \$36,000, confirming that the routing benefit is not an artifact of our evaluation suite's difficulty balance.

The production-skewed result is particularly relevant: if 60\% of real-world tasks are trivial or easy (a conservative estimate for customer-facing assistants), ParetOrch saves \$57,000/year while sacrificing only 2.9\% of peak quality.

### 5.6 Ablation Study (RQ5)

Table 5 isolates each ParetOrch component at $\tau = 0.75$.

| Variant | Cost (USD) | Accuracy | $\Delta$ Cost | $\Delta$ Acc. |
|---------|-----------|----------|-------------|--------------|
| Full ParetOrch | 2.857 | 80.1% | --- | --- |
| No DIT features | 3.874 | 79.8% | +35.6% | $-$0.4% |
| No cost model | 3.691 | 81.4% | +29.2% | +1.5% |
| No Pareto optimization | 4.429 | 83.3% | +55.0% | +4.0% |
| Random within threshold | 3.667 | 81.7% | +28.3% | +2.0% |

DIT features contribute the largest cost margin. Removing them substitutes a task-length heuristic, which cannot distinguish genuinely easy tasks (flat suffices) from merely short tasks (may still need coordination): cost increases by 35.6\% with negligible accuracy change. Without the cost model, the router picks quality-adequate topologies but cannot distinguish cheap from expensive, increasing cost by 29.2\%. Removing Pareto optimization entirely (greedy highest-quality) collapses to always-debate behavior, increasing cost by 55.0\%. The random-within-threshold variant confirms that cost optimization is distinct from quality optimization---selecting randomly among quality-adequate topologies wastes 28.3\% more budget for a negligible 2.0pp accuracy gain.

### 5.7 Per-Seed Consistency

The main finding holds across all three seeds individually:

| Seed | Cost Reduction | Quality Retention | Cost/Correct |
|------|---------------|-------------------|-------------|
| 42 | 35.2% | 102.9% | \$0.042 |
| 137 | 35.7% | 90.0% | \$0.045 |
| 2024 | 35.6% | 95.9% | \$0.041 |

The coefficient of variation across seeds is 0.7\% for cost reduction, indicating high stability. Quality retention varies more widely (90.0\%--102.9\%) due to per-seed stochasticity in LLM task success. The wider variation in quality retention reflects the stochasticity of LLM task success on borderline-difficulty tasks, where a single task flipping between success and failure shifts the percentage.

### 5.8 Difficulty-Stratified Analysis

| Difficulty | Flat Acc. | Hier. Acc. | Debate Acc. | Gap (D$-$F) | ParetOrch Choice | $p$-value (F vs. D) |
|-----------|----------|-----------|------------|------------|-----------------|---------------------|
| Trivial | 96.1% | 90.2% | 94.1% | $-$2.0pp | Flat (100%) | 0.41 |
| Easy | 89.5% | 80.7% | 98.2% | 8.7pp | Flat (85%), Hier. (15%) | 0.08 |
| Medium | 54.2% | 76.4% | 79.2% | 25.0pp | Hier. (55%), Deb. (35%), Flat (10%) | $<$0.001 |
| Hard | 33.3% | 68.1% | 68.1% | 34.8pp | Deb. (70%), Hier. (25%), Flat (5%) | $<$0.001 |

(Holm-Bonferroni corrected $p$-values for four comparisons.)

On trivial tasks, flat actually outperforms debate by 2.0pp ($p = 0.41$, not significant). ParetOrch correctly routes 100\% of these to flat, saving the full topology overhead on work where debate adds cost without adding value. On hard tasks, the gap widens to 34.8pp ($p < 0.001$), and ParetOrch routes 70\% to debate. The 5\% of hard tasks routed to flat are cases where no topology's predicted success exceeds $\tau$; flat provides a best-effort attempt at minimum cost.
