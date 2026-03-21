---
section: conclusion
word_count: 310
citation_count: 2
draft_version: 1
---

## Conclusion

ParetOrch reframes multi-agent cost optimization as a topology selection problem. Rather than routing queries to cheaper models within a fixed architecture \cite{chen2023frugalgpt, ong2024routellm}, our framework selects among structurally distinct orchestration topologies---flat, hierarchical, and debate---based on a task's measured decomposition and interaction requirements. This shift from model-level to topology-level optimization opens a qualitatively different region of the cost-quality Pareto frontier.

Three results anchor this contribution. First, the DIT (Decomposability, Iterativeness, Tool Diversity) framework provides a three-dimensional characterization of task complexity that predicts which topology delivers the best cost-quality tradeoff. Across 84 tasks evaluated with 3-seed replication (756 topology runs), DIT-guided routing achieves a 35.5% cost reduction relative to always-debate while retaining 96.2% of peak quality. Second, we identify a crossover point at DIT magnitude approximately 0.35: tasks below this threshold gain little from multi-agent coordination and route efficiently to flat single-agent execution, while tasks above it benefit from the structured reasoning that hierarchical and debate topologies provide. This threshold gives practitioners an actionable decision rule rather than requiring per-task tuning. Third, a single parameter $\tau$ controls the cost-quality operating point, enabling deployment teams to slide along the Pareto frontier as budget constraints shift.

Topology-aware optimization is orthogonal to model routing and composable with it. A system could first select a topology via DIT-guided routing, then select models within that topology using cost-aware model routers---jointly optimizing both axes. Beyond composition, several concrete extensions remain: online DIT estimation from partial task execution, expansion beyond three canonical topologies, and adaptive $\tau$ scheduling that tightens quality constraints when error rates spike. These directions would move multi-agent systems from static architectural choices toward runtime-adaptive orchestration that matches structural complexity to task demands.
