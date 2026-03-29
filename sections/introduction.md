---
section: introduction
word_count: 1050
citation_count: 10
draft_version: 2
---

## Introduction

Multi-agent LLM systems have a cost problem hiding in plain sight. A three-agent debate with two critique rounds consumes 3.5$\times$ the tokens of a single flat call---and the multiplier is structural, not a function of which model sits behind it. At GPT-4o pricing (\$2.50/1M input, \$10/1M output tokens), routing 10,000 daily tasks through debate instead of flat adds roughly \$376 per day, or \$137,000 per year, in coordination overhead alone. On trivial tasks---unit conversions, simple lookups, straightforward formatting---flat actually outperforms debate by 2.0 percentage points (96.1\% vs. 94.1\%), and the difference is not significant ($p = 0.41$). That is \$137,000 spent on noise.

The pattern reverses for hard tasks. When a coding challenge requires decomposition into subtasks, or a research question demands adversarial refinement, debate outperforms flat by 34.8 percentage points. The same topology that wastes money on easy work earns its cost on hard work. The problem is that most deployments pick one topology and apply it uniformly. The industry standard is architectural monogamy: run everything through MetaGPT's pipeline \cite{hong2024metagpt}, or ChatDev's waterfall \cite{qian2024chatdev}, or AutoGen's conversation framework \cite{wu2023autogen}. Nobody asks, per query, whether the expensive topology is worth it.

A related problem has attracted substantial attention. FrugalGPT \cite{chen2023frugalgpt} cascades queries through progressively expensive models, stopping when a confidence check signals adequacy. RouteLLM \cite{ong2024routellm} trains preference-based routers to select between strong and weak LLMs, cutting cost by 85\% on MT-Bench. HybridLLM \cite{ding2024hybrid} routes easy queries to small models and hard ones to large ones, reducing large-model calls by 40\%. AutoMix \cite{aggarwal2024automix} frames model selection as a POMDP with self-verification triggers. These systems all optimize the same decision: *which model should answer this query?* They treat the execution structure---a single call---as given and vary the model behind it.

That leaves the bigger lever untouched. Switching from GPT-4o to GPT-4o-mini saves roughly 2$\times$ on per-token cost. Switching from debate to flat on a task that does not need debate saves 3.5$\times$ on structural overhead---and the two savings axes are independent. A system that jointly selects model *and* topology navigates a cost surface with two orders of magnitude of variation, yet no prior work routes at the topology level with formal cost-quality guarantees.

We introduce ParetOrch, a framework that routes each incoming task to the cheapest orchestration topology meeting a configurable quality threshold $\tau$. ParetOrch works in three stages. First, it extracts DIT features---Decomposability ($D$), Iterativeness ($I$), and Tool Diversity ($T$)---from the task description, producing a compact structural fingerprint. Second, a DIT-conditioned logistic regression estimates the success probability of each candidate topology given those features. Third, a Pareto-aware selector filters to topologies whose predicted quality exceeds $\tau$ and picks the cheapest survivor. Routing overhead is under 5ms and zero LLM tokens.

The design rests on one empirical regularity: below DIT magnitude 0.35, flat topology is always Pareto-optimal. No amount of coordination overhead buys significant quality improvement on structurally simple tasks. Above 0.55, hierarchical dominates. Above 0.70, debate justifies its premium. This monotonic relationship between task complexity and optimal topology is what makes cost-aware routing tractable---and it connects to a classical idea. Herbert Simon's theory of near-decomposable systems \cite{simon1962complexity} predicts exactly this: tasks with low internal coupling (low $D$, low $I$, low $T$) resist decomposition because there is nothing to decompose. Multi-agent coordination adds cost without adding value. ParetOrch operationalizes this insight as a routing policy.

We evaluate ParetOrch on 84 tasks spanning four difficulty tiers, three topologies (flat, hierarchical, debate), and three random seeds, yielding 756 topology runs with 95\% bootstrap confidence intervals on all primary claims. The results:

1. \textbf{35.5\% cost reduction at 96.2\% quality retention.} ParetOrch cuts token cost by 35.5\% relative to always-debate while retaining 96.2\% of peak accuracy. For a deployment processing 10,000 tasks daily, this translates to estimated savings of \$68,000 annually. \emph{Falsifiable:} any replication with $>$100 tasks at comparable DIT coverage should observe $>$25\% cost reduction at $>$90\% quality retention.

2. \textbf{A topology cost algebra with $R^2 = 0.89$.} Multiplicative overhead multipliers ($\alpha_{\text{flat}} = 1.0$, $\alpha_{\text{hier}} = 2.3$, $\alpha_{\text{debate}} = 3.5$) predict actual token costs with no statistically significant interaction between difficulty tier and overhead ratio (Kruskal-Wallis $p = 0.34$). \emph{Falsifiable:} the cost ratios should replicate within $\pm 0.15$ on any topology implementation sharing the same agent count and round count.

3. \textbf{A crossover threshold at DIT magnitude $\sim$0.35.} Below this threshold, no topology upgrade is cost-justified: flat actually outperforms debate by 2.0pp on trivial tasks ($p = 0.41$). Above it, topology routing delivers monotonically increasing value. \emph{Falsifiable:} moving the crossover by $\pm 0.10$ should degrade cost-per-correct by $>$5\%.

4. \textbf{Single-parameter deployment interface.} The quality threshold $\tau$ sweeps a smooth, convex Pareto frontier from 50.0\% cost reduction (91.0\% retention at $\tau = 0.65$) to 3.8\% cost reduction (99.0\% retention at $\tau = 0.90$). Practitioners need no understanding of topology internals; $\tau$ is the only knob.

5. \textbf{Ablation-verified component necessity.} Removing DIT features increases cost by 35.6\%; removing the cost model increases cost by 29.2\%; removing Pareto optimization increases cost by 55.0\%. Each component is independently load-bearing.

Section 2 reviews cost-aware routing, multi-agent topologies, and Pareto optimization. Section 3 formalizes the topology selection problem and presents the ParetOrch algorithm. Section 4 describes the experimental setup. Sections 5 and 6 report results and discuss implications.
