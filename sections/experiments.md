---
section: experiments
word_count: 1100
citation_count: 4
draft_version: 2
---

## Experimental Setup

We designed experiments to answer five questions. Does per-query topology routing outperform static topology assignment (RQ1)? How close to the oracle Pareto frontier does ParetOrch get (RQ2)? How robust is the DIT crossover threshold at 0.35 (RQ3)? What happens under realistic (non-uniform) task distributions (RQ4)? And which ParetOrch components are independently necessary (RQ5)?

### 4.1 Task Suite and DIT Annotations

Our evaluation suite contains 84 tasks across four difficulty tiers: trivial (17 tasks), easy (19 tasks), medium (24 tasks), and hard (24 tasks). Tasks span 12 categories including coding, reasoning, research, summarization, and extraction. The distribution weights medium and hard tasks to stress-test topology differences where they matter most, while retaining enough trivial and easy tasks to measure cost savings on routine workloads.

Each task carries three DIT annotations on the $[0,1]$ scale. Two annotators independently scored all 84 tasks using operational definitions: $D$ is high when the task admits at least two independent subtasks whose solutions can be composed without cross-referencing; $I$ is high when iterative refinement (drafting, critiquing, revising) measurably improves output quality; $T$ is high when the task requires three or more distinct tool types (code execution, web search, file I/O, etc.). Inter-annotator agreement was $\kappa \geq 0.81$ on all three dimensions; disagreements were resolved by discussion. DIT magnitude increases monotonically across tiers: 0.07 (trivial), 0.34 (easy), 0.72 (medium), 1.10 (hard). Task design draws inspiration from GAIA \cite{mialon2023gaia}. DIT scores were assigned before any routing experiments to prevent outcome-driven annotation. All 84 task prompts, expected answers, and DIT annotations will be released upon publication.

### 4.2 Topology Configurations

All topologies use GPT-4o (2024-08-06) as the backbone. Holding the model constant isolates the effect of topology on cost and quality.

**Flat.** Single agent, full task prompt, one response. Cost multiplier: 1.0$\times$. Token consumption: 600 (trivial) to 5,600 (hard).

**Hierarchical.** Coordinator decomposes the task, delegates to 2--3 specialists, aggregates outputs. Cost multiplier: $\sim$2.3$\times$.

**Debate.** Two to three agents propose solutions, then engage in two rounds of structured critique. A judge synthesizes the final answer. Cost multiplier: $\sim$3.5$\times$.

### 4.3 Baselines

Five routing policies span the space from naive static assignment to accuracy-optimal routing:

- **Always-Flat:** cheapest possible; cost lower bound.
- **Always-Debate:** most expensive; quality upper bound.
- **Random:** uniform random topology per task; quantifies benefit of informed routing.
- **DIT Accuracy-Only:** DIT-based routing maximizing success rate without cost awareness. Tests whether cost-agnostic routing is already cost-efficient.
- **Majority-Class:** always selects the topology with highest training-set accuracy. In our data, debate achieves the highest overall accuracy, making this equivalent to Always-Debate; we omit it from Results Table 1 to avoid redundancy.

Each baseline runs across all 84 tasks with three seeds (42, 137, 2024). The ParetOrch quality predictor is trained with leave-one-out cross-validation: for each task, the logistic regression is fit on the remaining 83 tasks and evaluated on the held-out task. This ensures no train/test contamination in the routing accuracy claims.

### 4.4 Evaluation Metrics

Five metrics following the evaluation methodology of RouterBench \cite{hu2024routerbench}:

- **Total Cost:** sum of token costs in USD across all tasks (GPT-4o pricing: \$2.50/1M input, \$10/1M output).
- **Task Success Rate:** fraction of tasks answered correctly, averaged across seeds.
- **Cost per Correct Answer:** total cost divided by correct answers. Penalizes spending on tasks ultimately answered wrong.
- **Quality Retention:** $\text{Accuracy(policy)} / \text{Accuracy(always-debate)}$. Measures how much of the quality ceiling a policy preserves.
- **Cost Reduction:** $1 - \text{Cost(policy)} / \text{Cost(always-debate)}$. Fraction of cost saved vs. the most expensive baseline.

### 4.5 Statistical Methodology

All primary results report mean $\pm$ 95\% bootstrap confidence intervals across three seeds (10,000 bootstrap resamples per estimate). We use the percentile method for CI construction and verify coverage via simulation on synthetic data with known ground truth.

For pairwise topology comparisons, we use permutation tests ($n = 10{,}000$ permutations) rather than $t$-tests because the accuracy distributions are non-normal (bounded binary outcomes aggregated over tasks). We report Cohen's $d$ effect sizes alongside $p$-values for all claimed differences. For comparisons across multiple difficulty tiers (Table 2), we apply Holm-Bonferroni correction for four simultaneous tests. Practical significance is assessed by computing annual cost impact at 10,000 tasks/day: a difference must exceed \$5,000/year to be flagged as practically significant.

### 4.6 Sensitivity Analysis Design

To test RQ3 (crossover robustness), we perturb the DIT crossover threshold by $\pm 0.05$, $\pm 0.10$, and $\pm 0.15$ around the empirical value of 0.35 and measure the impact on cost-per-correct. If the crossover is sharp, small perturbations should degrade performance substantially; if it is gradual, the system should be robust to misspecification.

### 4.7 Realistic Workload Simulation

To address RQ4, we simulate three workload distributions beyond the uniform distribution of our task suite:

- **Production-skewed:** 60\% trivial/easy, 30\% medium, 10\% hard---reflecting a typical coding assistant where most queries are straightforward.
- **Research-heavy:** 20\% trivial/easy, 30\% medium, 50\% hard---reflecting an analysis pipeline where most tasks require multi-step reasoning.
- **Bimodal:** 45\% trivial, 10\% easy/medium, 45\% hard---reflecting a system that handles both routine queries and complex projects with little in between.

For each distribution, we resample (with replacement) from our 84-task corpus to create 1,000-task synthetic workloads and compute ParetOrch's cost reduction and quality retention. This tests whether the savings observed on our balanced evaluation suite generalize to realistic deployment skews.

### 4.8 Implementation Details

The full experiment comprises 84 tasks $\times$ 3 topologies $\times$ 3 seeds $= 756$ topology evaluation runs, plus routing evaluation across 6 policies (5 baselines + ParetOrch at 6 $\tau$ values). Temperature is 1.0 (GPT-4o default). Each topology enforces a 128K token generation budget. ParetOrch's logistic regression is fit via L-BFGS with $\ell_2$ regularization ($C = 1.0$) on training data from a 70/30 stratified split, preserving difficulty tier proportions. DIT feature extraction uses a bag-of-patterns classifier (subtask markers, conditional logic indicators, tool keywords) requiring zero LLM calls.
