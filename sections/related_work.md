---
section: related_work
word_count: 1180
citation_count: 24
draft_version: 2
---

## Related Work

The history of making LLM systems cheaper is a history of optimizing the wrong variable---and then discovering there was a bigger one underneath. We trace this progression through three threads that converge on the gap ParetOrch fills.

### 2.1 First, People Routed Between Models

The earliest cost optimization insight was simple: not every query needs the most expensive model. FrugalGPT \cite{chen2023frugalgpt} formalized this with an LLM cascade that starts cheap and escalates on low confidence, achieving up to 98\% cost reduction while matching GPT-4 quality. The key mechanism---a DistilBERT stop judger that predicts when a cheaper model's answer suffices---established the template that every subsequent routing system follows: estimate difficulty, then match resource to need.

RouteLLM \cite{ong2024routellm} replaced heuristic confidence with learned routing, training on human preference data to select between strong and weak LLMs and cutting cost by 85\% on MT-Bench with generalization to unseen model pairs. HybridLLM \cite{ding2024hybrid} added quality-gap awareness, reducing large-model invocations by 40\% with under 1\% quality loss. AutoMix \cite{aggarwal2024automix} cast the decision as a POMDP using self-verification confidence, halving costs on reasoning tasks. EcoAssistant \cite{zhang2023ecoassistant} combined hierarchical model cascading with solution retrieval, surpassing GPT-4 accuracy at half the cost.

More recent systems pushed the sophistication further. TREACLE \cite{treacle2024} treated cascade learning as reinforcement learning under joint monetary and latency constraints. C3PO \cite{c3po2025} introduced the first probabilistic cost bounds through conformal prediction. OptLLM \cite{liu2024optllm} formulated routing as multi-objective optimization with Pareto-dominant solutions, saving 2.4--49.2\% at equivalent accuracy. A recent survey \cite{dynamic_routing_survey2026} maps six routing strategies into a unified design framework.

Every one of these systems optimizes the same structural decision: which model answers this query. The execution pattern is always a single call; only the model behind it varies. This optimization axis is real and valuable---but it is not the dominant one once multi-agent systems enter the picture.

### 2.2 Then, People Built Multi-Agent Topologies

In parallel, a separate community was building coordination architectures without asking what they cost. MetaGPT \cite{hong2024metagpt} encodes standard operating procedures into role-specialized pipelines, using structured artifacts to reduce error propagation. ChatDev \cite{qian2024chatdev} implements a waterfall process where agents assume CEO, CTO, and programmer roles. AutoGen \cite{wu2023autogen} provides a flexible conversation framework supporting arbitrary multi-agent patterns. Du et al.\ \cite{du2023debate} demonstrated that multi-agent debate improves factuality and reasoning through adversarial consensus. Voyager \cite{wang2023voyager} explored the alternative of a single agent with a growing skill library.

Two concurrent developments brought cost awareness closer to topology design. DAAO \cite{su2025daao} introduced difficulty-aware agent orchestration with a VAE-based difficulty estimator and modular operator allocation, achieving state-of-the-art on six benchmarks at 64\% of inference cost. Dang and Qian \cite{dang2025evolving} proposed evolving orchestration through RL-trained policies that dynamically sequence agents. Both represent steps toward adaptive orchestration---but DAAO operates at the operator level (which agent performs each subtask), not the topology level (which coordination pattern structures the interaction). And neither models the cost-quality Pareto frontier that would let a practitioner dial between savings and accuracy.

The pattern across this thread is consistent: topology is chosen at design time and applied uniformly. MetaGPT always pipelines. ChatDev always waterfalls. Debate systems always debate. The question "given this specific task, is the expensive topology worth it?" never gets asked---because answering it requires a cost model of topology structure and a quality model conditioned on task characteristics. Neither existed before this work.

### 2.3 Nobody Connected the Two Threads

The gap sits at the intersection: cost-aware routing selects models but ignores topology. Topology design builds coordination patterns but ignores cost. The two communities cite different literatures, attend different workshops, and optimize different variables.

Pareto optimization provides the mathematical bridge. NSGA-II \cite{deb2002nsga2} established fast non-dominated sorting as the standard for evolutionary multi-objective optimization. Jin and Sendhoff \cite{jin2008pareto_ml} showed that machine learning problems are inherently multi-objective and that Pareto approaches outperform scalar cost functions when objectives conflict. In the bandit setting, contextual Thompson Sampling \cite{agrawal2013thompson} provides theoretical guarantees for arm selection conditioned on features---directly applicable to topology selection conditioned on task difficulty. Recent work has applied Pareto thinking to LLM systems: OptLLM maps cost-accuracy Pareto frontiers for model cascades; compute-accuracy frontiers \cite{compute_pareto2024} have been characterized for open-source reasoning models under varying test-time budgets.

But all existing Pareto optimization for LLMs operates at the model or inference level. Nobody has mapped the Pareto frontier across orchestration topologies, where cost differences are structural (determined by agent count, round count, and communication pattern) rather than per-token (determined by model pricing).

### 2.4 ParetOrch Bridges the Gap

ParetOrch differs from all prior work along three axes that jointly define a new design point.

First, it routes at the topology level rather than the model level. The decision is which coordination pattern to deploy (flat, hierarchical, debate), not which LLM to invoke within a fixed pattern. This is a strictly harder optimization because topology determines a cost multiplier (1.0$\times$ to 3.5$\times$) that compounds with whatever model cost sits underneath it.

Second, it provides analytical cost predictions before execution. The topology cost algebra derives overhead multipliers from structural parameters alone---agent count, round count, context propagation pattern---whereas budget-aware systems like BAMAS \cite{zhuo2025bamas} learn costs empirically and enforce static budgets that cannot adapt per-query.

Third, the quality threshold mechanism $\tau$ gives practitioners a single parameter to sweep the entire Pareto frontier, from aggressive cost-cutting ($\tau = 0.65$: 50.0\% savings, 91.0\% quality retention) to conservative quality-first operation ($\tau = 0.90$: 3.8\% savings, 99.0\% retention). Where model routing answers "which model?", topology optimization answers "which structure offline?", and token pruning answers "how to trim waste within a structure?", ParetOrch answers the question none of them address: which coordination pattern for this specific task, right now, at minimum cost?

The two routing axes---model selection and topology selection---are orthogonal and composable. An organization could cascade models via FrugalGPT to pick the cheapest adequate LLM, then route topologies via ParetOrch to pick the cheapest adequate coordination pattern, stacking savings along independent dimensions. We leave joint model-topology routing to future work (Section 6.4) but note that the cost savings multiply rather than add.
