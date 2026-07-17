# ParetOrch

_Cost-Quality Pareto Optimization for Multi-Agent Topology Selection_

> A Pareto-optimal routing policy that jointly considers task success probability and API cost. Reduces multi-agent orchestration spending by **35.5%** versus always-debate while keeping 92.1% of peak success rate.

**Target venue:** [NeurIPS 2026](https://neurips.cc/Conferences/2026)  •  **Status:** Submission package compiled (PDF: 679 KB)

---

## Headline Numbers

| Metric | Value |
|---|---|
| Cost reduction | 35.5% vs. always-debate |
| Quality preservation | 92.1% of peak success rate |
| Routing decision threshold | Tunable τ for safety-critical applications |
| Backbone | Claude Opus 4.6 with cost-quality Pareto frontier characterization |

## Abstract (excerpt)

Multi-agent topology choices have wildly different cost profiles, yet practitioners typically pick the topology with the highest mean accuracy. ParetOrch routes per-task: cheap topologies handle the easy distribution, expensive ones earn their cost on hard tasks where the quality gap is large.

## Reproducibility

```bash
# Clone
git clone https://github.com/jrajath94/paper4_paretorch.git
cd paper4_paretorch

# Recompile the PDF (needs Tectonic or pdflatex)
tectonic main.tex   # produces main.pdf

# Browse the materials:
#   output/sections/   — per-section markdown + .tex
#   output/figures/    — figures (PDF + PNG)
#   experiments/       — task suite + results JSON
```


## Repository Layout

```
paper4_paretorch/
├── README.md              ← you are here
├── paper.pdf              ← compiled PDF
├── output/                ← LaTeX source, sections, figures, bibliography
│   ├── paper.tex / main.tex
│   ├── sections/
│   ├── figures/
│   └── bibliography.bib / references.bib
├── experiments/           ← task suite + results data
├── scripts/               ← pipeline scripts (literature retrieval, citation verification, etc.)
├── state/                 ← paper state and pipeline log
└── venues/                ← venue configs (NeurIPS / TMLR / JAIR formatting rules)
```

## Tooling

This paper was developed with a custom multi-agent research pipeline using:
- **Claude Opus 4.6 / Sonnet 4.6** (via [Claude Code](https://www.anthropic.com/claude-code)) — main author + reviewer agents
- **MiniMax-M2.7** — bulk-pass review and ideation calls
- **Tectonic** — LaTeX compilation
- **Semantic Scholar / OpenAlex / CrossRef** APIs for citation verification

## Part of the Multi-Agent Orchestration paper series

| # | Repo | Title | Venue |
|---|---|---|---|
| 1 | [`paper1_orchestrabench`](https://github.com/jrajath94/paper1_orchestrabench) | OrchestraBench: which topology fits which task? | NeurIPS 2026 |
| 2 | [`paper2_cost_routing`](https://github.com/jrajath94/paper2_cost_routing) | Cost-Aware Topology Routing | NeurIPS 2026 (W) |
| 3 | [`paper3_failure_planning`](https://github.com/jrajath94/paper3_failure_planning) | Failure-Aware Planning for LLM Agents | TMLR |
| 4 | [`paper4_paretorch`](https://github.com/jrajath94/paper4_paretorch) | ParetOrch: Cost-Quality Pareto Optimization | NeurIPS 2026 |
| 5 | [`paper5_adaptswitch`](https://github.com/jrajath94/paper5_adaptswitch) | AdaptSwitch: Runtime Topology Switching | JAIR |

## License

Code: MIT.  Paper text and figures: CC BY 4.0.

## Citation

If you use this work, please cite (BibTeX entries to be finalized at submission):

```bibtex
@article{paper4_paretorch_2026,
  title  = {ParetOrch: Cost-Quality Pareto Optimization for Multi-Agent Topology Selection},
  author = {Rajath, J.},
  year   = {2026},
  journal= {Under review at NeurIPS 2026},
}
```
