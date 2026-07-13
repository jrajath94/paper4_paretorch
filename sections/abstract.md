---
section: abstract
word_count: 195
citation_count: 0
draft_version: 1
---

## Abstract

Multi-agent LLM systems deploy distinct orchestration topologies---flat, hierarchical, and debate---that differ in both task success rate and API token cost. Existing cost-aware routing selects between cheap and expensive *models* but ignores the cost-quality tradeoff across *topologies*. We introduce ParetOrch, a Pareto-optimal routing framework that selects the cheapest orchestration topology meeting a configurable quality threshold. ParetOrch operates in three stages: (1) extract DIT (Decomposability, Iterativeness, Tool Diversity) features from incoming tasks, (2) predict per-topology success probability via a DIT-conditioned quality model, and (3) select the cheapest topology whose predicted quality exceeds a user-specified threshold $\tau$. We evaluate on 84 tasks across four difficulty tiers, three topologies, and three random seeds (756 topology runs). ParetOrch reduces orchestration cost by 35.5\% relative to always-debate while retaining 92.1\% of peak task success rate. Ablation shows DIT features contribute the largest margin: removing them increases cost by 35.6\%. We identify a crossover at DIT magnitude ${\sim}0.35$, below which flat topology is always Pareto-optimal---a practitioner rule of thumb that captures most routing benefit. Code and data are available at [repository URL].
