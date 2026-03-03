---
section: method
title: "ParetOrch: Pareto-Optimal Topology Routing"
word_count: 1650
citation_count: 5
draft_version: 2
---

## ParetOrch: Pareto-Optimal Topology Routing

This section formalizes topology selection as a constrained optimization problem, derives cost and quality models from execution data, and presents the ParetOrch routing algorithm with a theoretical savings bound.

### 3.1 Problem Formulation

Consider a task $x$ drawn from a distribution $\mathcal{X}$ over agentic workloads. Each task is characterized by its DIT feature vector $\mathbf{d}(x) = (D, I, T) \in [0,1]^3$, where $D$ measures Decomposability (the degree to which $x$ decomposes into independent subtasks), $I$ measures Iterativeness (the benefit of sequential refinement passes), and $T$ measures Tool Diversity (the breadth of external tools required). These three axes capture the structural properties of a task that determine how much coordination overhead it demands---and they connect to a classical idea. Simon's theory of near-decomposable systems \cite{simon1962complexity} predicts that systems with weak inter-component coupling can be analyzed component-by-component without loss. A task with low $D$, low $I$, and low $T$ is exactly such a system: it resists multi-agent decomposition because its internal structure is monolithic. Multi-agent coordination adds message-passing cost without exploiting any structural advantage.

We define DIT magnitude as $\|\mathbf{d}\| = \sqrt{D^2 + I^2 + T^2}$, which serves as a scalar summary of structural complexity. DIT magnitude ranges from 0 (a trivial lookup) to $\sqrt{3} \approx 1.73$ (a task maximally demanding on all three axes).

Let $\mathcal{A} = \{\texttt{flat}, \texttt{hier}, \texttt{debate}\}$ denote the set of candidate orchestration topologies. For task $x$ and topology $a \in \mathcal{A}$, define:

- $c(a, x) \in \mathbb{R}_{+}$: the token cost of executing $x$ under topology $a$,
- $q(a, x) \in \{0, 1\}$: a binary indicator of task success.

The topology selection problem is:

$$a^{*} = \arg\min_{a \in \mathcal{A}} \; c(a, x) \quad \text{s.t.} \quad P\bigl(q(a, x) = 1 \mid \mathbf{d}(x)\bigr) \geq \tau$$

where $\tau \in [0, 1]$ is a user-specified quality threshold. This formulation treats topology selection as a contextual decision problem: DIT features serve as context, topologies serve as arms, and the objective minimizes cost subject to a quality floor. The departure from standard contextual bandits \cite{agrawal2013thompson} is that we minimize cost subject to a quality constraint rather than maximizing reward subject to a budget.

### 3.2 Topology Cost Algebra

Each topology induces a token-cost profile determined by its communication structure. We model cost as a topology-specific multiplier applied to a task-dependent base cost:

$$c(a, x) = \alpha_a \cdot c_{\text{base}}(x)$$

where $c_{\text{base}}(x) = p_{\text{in}} \cdot n_{\text{in}}(x) + p_{\text{out}} \cdot n_{\text{out}}(x)$ is the cost of a single flat execution (input/output token counts times per-token prices), and $\alpha_a$ is the topology overhead multiplier.

**Flat.** A single agent receives the full prompt and produces one response. No coordination overhead: $\alpha_{\texttt{flat}} = 1.0$.

**Hierarchical.** A coordinator decomposes the task and delegates to 2--3 specialists, then aggregates their outputs. The coordinator's decomposition and synthesis passes, plus specialist execution, yield $\alpha_{\texttt{hier}} \approx 2.3$. The multiplier is approximately linear in agent count because the tree structure keeps branches independent---costs add rather than multiply.

**Debate.** Two to three agents propose competing solutions over $R = 2$ rounds; a judge synthesizes the final answer. Each round requires full context propagation across agents, producing $\alpha_{\texttt{debate}} \approx 3.5$.

The multiplicative model is deliberately simple, and its simplicity is a feature: the overhead ratio depends on topology structure (agent count, round count, context propagation pattern), not on the specific task or backbone model. This means $\alpha_a$ can be estimated once per topology configuration and reused across all tasks.

How well does this hold? Across 756 topology runs (84 tasks $\times$ 3 topologies $\times$ 3 seeds), observed overhead ratios were $2.30 \pm 0.18$ for hierarchical and $3.51 \pm 0.31$ for debate relative to flat. A Kruskal-Wallis test found no significant interaction between overhead ratio and difficulty tier ($p = 0.34$), confirming that the multipliers are task-independent within our evaluation range. The multiplicative model explains 89\% of cost variance ($R^2 = 0.89$); the residual 11\% comes from variable judge verbosity in debate and variable decomposition depth in hierarchical topologies.

The cost gap between topologies is the source of routing value. On trivial tasks, flat costs \$0.003 per task while debate costs \$0.012. On hard tasks, flat costs \$0.030 while debate costs \$0.103. Both gaps are roughly 3.5$\times$, but the absolute dollar amounts scale with task complexity, making routing decisions more consequential on expensive workloads.

### 3.3 DIT-Conditioned Quality Prediction

ParetOrch needs to predict, before execution, whether a task requires an expensive topology. We train topology-specific logistic regressions:

$$P\bigl(q(a, x) = 1 \mid \mathbf{d}(x)\bigr) = \sigma\bigl(\mathbf{w}_a^{\top} \phi(\mathbf{d}(x)) + b_a\bigr)$$

where $\sigma$ is the sigmoid function and $\phi(\mathbf{d})$ concatenates: (1) raw DIT components $(D, I, T)$, (2) DIT magnitude $\|\mathbf{d}\|$, and (3) pairwise interactions $(D \cdot I, D \cdot T, I \cdot T)$---a 7-dimensional feature vector per task. Separate coefficient vectors $\mathbf{w}_a$ for each topology produce three calibrated probability estimates.

Why logistic regression rather than gradient-boosted trees or neural networks? Three reasons. The feature space is 7-dimensional, making logistic regression well-calibrated without extensive tuning. The learned coefficients are directly interpretable: a positive $w_D$ for hierarchical confirms that decomposable tasks benefit from planner-worker patterns. And probability calibration matters more than discriminative accuracy for downstream Pareto optimization; logistic regression provides calibrated probabilities by construction, whereas tree ensembles require post-hoc calibration (Platt scaling or isotonic regression) that adds complexity and potential miscalibration on small datasets.

The empirical regularity that makes this work: below DIT magnitude 0.35, flat topology matches or exceeds debate accuracy (within 2pp). The quality gap opens with DIT magnitude. At $\|\mathbf{d}\| > 0.55$, hierarchical outperforms flat by 22.2pp (medium difficulty: 76.4\% vs. 54.2\%). At $\|\mathbf{d}\| > 0.70$, debate outperforms flat by 34.8pp (hard difficulty: 68.1\% vs. 33.3\%). This monotonic structure means even a simple classifier can separate the regimes where topology choice matters from those where it does not.

### 3.4 Pareto-Aware Topology Selection

Given quality predictions $\hat{q}_a$ and cost estimates $\hat{c}_a$ for each topology, ParetOrch selects in two phases:

**Phase 1: Feasibility filtering.** Compute the candidate set $\mathcal{A}_{\tau} = \{a \in \mathcal{A} : \hat{q}_a \geq \tau\}$.

**Phase 2: Cost minimization.** If $\mathcal{A}_{\tau} \neq \emptyset$, select $a^{*} = \arg\min_{a \in \mathcal{A}_{\tau}} \hat{c}_a$. If $\mathcal{A}_{\tau} = \emptyset$, fall back to $a^{*} = \arg\max_{a \in \mathcal{A}} \hat{q}_a$.

This procedure always selects a Pareto-optimal topology from the feasible set: among topologies meeting the quality floor, the cheapest cannot be dominated. The threshold $\tau$ directly controls the practitioner's position on the Pareto frontier.

**Algorithm 1: ParetOrch Topology Routing**

```
Input: Task x, quality threshold tau, cost multipliers {alpha_a}, quality parameters {w_a, b_a}
Output: Selected topology a*

1. Extract DIT features: d(x) <- DIT_Extract(x)
2. Compute feature expansion: phi(d) <- (D, I, T, ||d||, D*I, D*T, I*T)
3. For each topology a in A:
     q_hat_a <- sigma(w_a^T phi(d) + b_a)    // predicted success probability
     c_hat_a <- alpha_a * c_base(x)            // estimated cost
4. A_tau <- {a in A : q_hat_a >= tau}           // feasibility filter
5. If A_tau non-empty:
     a* <- argmin_{a in A_tau} c_hat_a          // cheapest feasible
6. Else:
     a* <- argmax_{a in A} q_hat_a              // best-effort fallback
7. Return a*
```

Routing overhead: one lightweight classifier pass for DIT extraction, three logistic regression evaluations over 7-dimensional inputs, three multiplications for cost estimation. Total latency under 5ms---four orders of magnitude below typical LLM inference.

### 3.5 Theoretical Savings Bound

Let $\alpha_{\max} = \max_{a \in \mathcal{A}} \alpha_a$ and $\alpha_{\min} = \min_{a \in \mathcal{A}} \alpha_a$ denote the most and least expensive topology multipliers. Let $\epsilon$ denote the quality predictor's miscalibration: $|\hat{q}_a - q_a| \leq \epsilon$ for all $a, x$.

**Proposition 1.** For any task distribution $\mathcal{X}$ and threshold $\tau$, the cost of ParetOrch satisfies:

$$\mathbb{E}[c_{\text{ParetOrch}}] \leq \mathbb{E}[c_{\text{oracle}}] + \frac{\alpha_{\max}}{\alpha_{\min}} \cdot \epsilon \cdot \mathbb{E}[c_{\text{base}}]$$

where $c_{\text{oracle}}$ selects the cheapest topology that actually succeeds on each task with perfect hindsight.

*Proof sketch.* When the quality predictor is correct (probability $1 - \epsilon$), ParetOrch makes the same selection as the constrained oracle. When miscalibrated, the worst case routes to the most expensive topology when the cheapest would have sufficed, paying excess $(\alpha_{\max} / \alpha_{\min}) \cdot c_{\text{base}}$ per misrouted task.

In our setting: $\alpha_{\max}/\alpha_{\min} = 3.5$, $\epsilon \approx 0.05$ (from calibration analysis in Section 5.5), and mean $c_{\text{base}} \approx \$0.015$. The bound predicts excess cost of $\sim$\$0.003 per task, or roughly 6\% over oracle.
