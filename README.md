# ParetOrch

_Cost-Quality Pareto Optimization for Multi-Agent Topology Selection_

> A Pareto-optimal routing policy that jointly considers task success probability and API cost. Reduces multi-agent orchestration spending by **40-60%** versus always-best-topology while keeping ≥95% of peak success rate.

**Target venue:** [NeurIPS 2026](https://neurips.cc/Conferences/2026)  •  **Status:** Submission package compiled (PDF: 679 KB)

---

## Headline Numbers

| Metric | Value |
|---|---|
| Cost reduction | 40-60% vs. always-best-topology |
| Quality preservation | ≥95% of peak success rate |
| Routing decision threshold | Tunable τ for safety-critical applications |
| Backbone | Claude Opus 4.6 with cost-quality Pareto frontier characterization |

## Abstract (excerpt)

Multi-agent topology choices have wildly different cost profiles, yet practitioners typically pick the topology with the highest mean accuracy. ParetOrch routes per-task: cheap topologies handle the easy distribution, expensive ones earn their cost on hard tasks where the quality gap is large.

## Independent Review (Sakana AI Scientist v2)

This paper has been reviewed by [Sakana AI Scientist v2](https://github.com/SakanaAI/AI-Scientist-v2)'s `perform_llm_review` module via the MiniMax-M2.7 backend. The review uses NeurIPS-style reviewer guidelines.

| Metric | Score |
|---|---|
| Overall | **6** / 10 |
| Decision | **Accept** |
| Soundness | 3 / 4 |
| Confidence | 4 / 5 |

**Top weaknesses identified:**

- Simulated costs from token counting rather than real API billing - significant gap for practical deployment
- Simple logistic regression predictor for quality estimation; neural approaches could improve accuracy
- Limited scope: only 3 topologies and single base model (GPT-4o)
- DIT feature extraction process not adequately detailed; mentions 'one classifier pass' without specifics
- Moderate cross-validated routing accuracy (82.1%) - routing failures could degrade quality

> _Full review JSON: [`ai_scientist/reviews/minimax_review.json`](ai_scientist/reviews/minimax_review.json)._

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
#   output/reviews/    — citation, data, math, figure, AI-pattern audits
#   experiments/       — task suite + results JSON
#   ai_scientist/      — Sakana AI Scientist v2 review pipeline outputs
```


## Repository Layout

```
paper4_paretorch/
├── README.md              ← you are here
├── paper.pdf              ← compiled PDF
├── output/                ← LaTeX source, sections, figures, reviews, bibliography
│   ├── paper.tex / main.tex
│   ├── sections/
│   ├── figures/
│   ├── reviews/           (audits: citation, data, math, figure, consistency, AI patterns)
│   └── bibliography.bib / references.bib
├── experiments/           ← task suite + results data
├── scripts/               ← pipeline scripts (literature retrieval, citation verification, etc.)
├── ai_scientist/          ← Sakana AI Scientist v2 outputs (independent review JSON)
├── state/                 ← paper state and pipeline log
└── venues/                ← venue configs (NeurIPS / TMLR / JAIR formatting rules)
```

## Tooling

This paper was developed with a custom multi-agent research pipeline using:
- **Claude Opus 4.6 / Sonnet 4.6** (via [Claude Code](https://www.anthropic.com/claude-code)) — main author + reviewer agents
- **Sakana AI Scientist v2** — independent NeurIPS-style review
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
