# Citation Verification Audit

**Paper:** ParetOrch: Cost-Quality Pareto Optimization for Multi-Agent Topology Selection
**Date:** 2026-03-17
**Auditor:** Citation Verification Agent (Opus 4.6)
**Method:** Each citation checked against arXiv, Semantic Scholar, Google Scholar, conference proceedings, and publisher sites.

---

## Summary

| Status | Count |
|--------|-------|
| VERIFIED | 18 |
| MINOR ERROR | 4 |
| SUSPECT | 3 |
| FABRICATED | 0 |
| **Total** | **25** |

**Overall assessment:** No fabricated citations detected. 18 of 25 citations are fully verified with correct metadata. 4 citations have minor metadata issues (wrong venue year, incomplete author lists, or slightly incorrect author names). 3 citations have significant metadata problems that require correction (placeholder author names, wrong first-author attribution).

---

## Per-Citation Verification

### 1. `agrawal2013contextual_ts` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | S. Agrawal and N. Goyal | S. Agrawal and N. Goyal |
| Title | Thompson sampling for contextual bandits with linear payoffs | Thompson Sampling for Contextual Bandits with Linear Payoffs |
| Venue | ICML, 2013 | ICML 2013 (Proceedings of the 30th ICML) |
| Source | [MLR Press](https://proceedings.mlr.press/v28/agrawal13.html), [arXiv:1209.3352](https://arxiv.org/abs/1209.3352) |

**Status: VERIFIED.** All metadata matches.

---

### 2. `aggarwal2024automix` -- MINOR ERROR

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | P. Aggarwal, A. Madaan, A. Anand, and S. P. Potharaju | Pranjal Aggarwal, Aman Madaan, Ankit Anand, Srividya Pranavi Potharaju, +9 more authors |
| Title | AutoMix: Automatically mixing language models | AutoMix: Automatically Mixing Language Models |
| Venue | NeurIPS, 2024 | NeurIPS 2024 |
| Source | [NeurIPS 2024 proceedings](https://proceedings.neurips.cc/paper_files/paper/2024/hash/ecda225cb187b40ea8edc1f46b03ffda-Abstract-Conference.html), [arXiv:2310.12963](https://arxiv.org/abs/2310.12963) |

**Status: MINOR ERROR.** Paper is real and venue/year are correct. However, the author abbreviation "S. P. Potharaju" is misleading -- the author's name is "Srividya Pranavi Potharaju" (not initials S. P.). Additionally, the bibitem lists only 4 of 13 authors without "et al." This is acceptable practice but the last listed author's name abbreviation is nonstandard.

**Recommended fix:** Change "S.~P. Potharaju" to "S.~P. Potharaju et~al." or spell out as "Srividya~Pranavi Potharaju".

---

### 3. `c3po2025` -- SUSPECT

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | C3PO Authors | A. Valkanas, S. Pal, P. Rumiantsev, Y. Zhang, M. Coates |
| Title | C3PO: Optimized large language model cascades with probabilistic cost constraints for reasoning | C3PO: Optimized Large Language Model Cascades with Probabilistic Cost Constraints for Reasoning |
| Venue | NeurIPS, 2025 | NeurIPS 2025 |
| Source | [NeurIPS 2025 poster](https://neurips.cc/virtual/2025/loc/san-diego/poster/116958), [arXiv:2511.07396](https://arxiv.org/abs/2511.07396) |

**Status: SUSPECT.** The paper itself is REAL and the venue/year are correct. However, the bibitem uses the **placeholder** "C3PO Authors" instead of actual author names. This is unacceptable for submission. The actual authors are: Antonios Valkanas, Soumyasundar Pal, Pavel Rumiantsev, Yingxue Zhang, and Mark Coates.

**MUST FIX:** Replace "C3PO Authors" with actual author names:
```
A.~Valkanas, S.~Pal, P.~Rumiantsev, Y.~Zhang, and M.~Coates.
```

---

### 4. `chen2023frugalgpt` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | L. Chen, M. Zaharia, and J. Zou | Lingjiao Chen, Matei Zaharia, James Zou |
| Title | FrugalGPT: How to use large language models while reducing cost and improving performance | FrugalGPT: How to Use Large Language Models While Reducing Cost and Improving Performance |
| Venue | arXiv preprint arXiv:2305.05176, 2023 | arXiv:2305.05176, May 2023 |
| Source | [arXiv:2305.05176](https://arxiv.org/abs/2305.05176) |

**Status: VERIFIED.** All metadata matches.

---

### 5. `dang2025evolving` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | Y. Dang, C. Qian, et al. | Yufan Dang, Chen Qian, +12 authors |
| Title | Multi-agent collaboration via evolving orchestration | Multi-Agent Collaboration via Evolving Orchestration |
| Venue | arXiv preprint arXiv:2505.19591, 2025 | arXiv:2505.19591, May 2025 (accepted NeurIPS 2025) |
| Source | [arXiv:2505.19591](https://arxiv.org/abs/2505.19591), [NeurIPS 2025 poster](https://neurips.cc/virtual/2025/loc/san-diego/poster/118584) |

**Status: VERIFIED.** All metadata matches. Note: paper has since been accepted at NeurIPS 2025, so the venue could be updated from "arXiv preprint" to "NeurIPS, 2025" for a stronger citation.

---

### 6. `deb2002nsga2` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | K. Deb, A. Pratap, S. Agarwal, and T. Meyarivan | K. Deb, A. Pratap, S. Agarwal, T. Meyarivan |
| Title | A fast and elitist multiobjective genetic algorithm: NSGA-II | A Fast and Elitist Multiobjective Genetic Algorithm: NSGA-II |
| Venue | IEEE Trans. Evolutionary Computation, 6(2):182-197, 2002 | IEEE Trans. Evolutionary Computation, 6(2):182-197, 2002 |
| Source | [IEEE Xplore](https://ieeexplore.ieee.org/document/996017/) |

**Status: VERIFIED.** All metadata matches exactly.

---

### 7. `dekoninck2024unified` -- MINOR ERROR

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | J. Dekoninck, M. Baader, and M. Vechev | Jasper Dekoninck, Maximilian Baader, Martin Vechev |
| Title | A unified approach to routing and cascading for LLMs | A Unified Approach to Routing and Cascading for LLMs |
| Venue | arXiv preprint arXiv:2410.10347, 2024 | arXiv:2410.10347, Oct 2024 (accepted ICML 2025 / ICLR 2025) |
| Source | [arXiv:2410.10347](https://arxiv.org/abs/2410.10347), [ICML 2025 poster](https://icml.cc/virtual/2025/poster/46183) |

**Status: MINOR ERROR.** Paper is real and all listed metadata is accurate for when it was cited. However, it has since been accepted to ICML 2025 (and appears at ICLR 2025 as well), so the venue could be updated for a stronger reference.

---

### 8. `ding2024hybrid` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | D. Ding, A. Mallick, C. Wang, R. Sim, and S. Mukherjee | Dujian Ding, Ankur Mallick, Chi Wang, Robert Sim, Subhabrata Mukherjee, +3 more |
| Title | Hybrid LLM: Cost-efficient and quality-aware query routing | Hybrid LLM: Cost-Efficient and Quality-Aware Query Routing |
| Venue | ICLR, 2024 | ICLR 2024 |
| Source | [ICLR 2024 proceedings](https://proceedings.iclr.cc/paper_files/paper/2024/file/b47d93c99fa22ac0b377578af0a1f63a-Paper-Conference.pdf), [arXiv:2404.14618](https://arxiv.org/abs/2404.14618) |

**Status: VERIFIED.** All metadata matches.

---

### 9. `du2023debate` -- MINOR ERROR

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | Y. Du, S. Li, A. Torralba, J. B. Tenenbaum, and I. Mordatch | Yilun Du, Shuang Li, Antonio Torralba, Joshua B. Tenenbaum, Igor Mordatch |
| Title | Improving factuality and reasoning in language models through multiagent debate | Improving Factuality and Reasoning in Language Models through Multiagent Debate |
| Venue (bibitem) | ICML, 2024 | ICML 2024 (Proceedings of the 41st ICML) |
| Citation key | du2023debate | Should arguably be du2024debate |
| Source | [ICML 2024 proceedings](https://proceedings.mlr.press/v235/du24e.html), [arXiv:2305.14325](https://arxiv.org/abs/2305.14325) |

**Status: MINOR ERROR.** The paper is real. The venue "ICML, 2024" is correct in the bibitem text. However, the **citation key** uses `2023` (from the arXiv preprint date) while the bibitem body says "2024" (the conference publication year). The bibitem body is correct -- the paper was published at ICML 2024. The citation key `du2023debate` is a cosmetic issue only (keys are arbitrary identifiers), but it could confuse readers who infer year from the key. No year field mismatch in the actual entry.

---

### 10. `dynamic_routing_survey2026` -- SUSPECT

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | Survey Authors | Yasmin Moslem and John D. Kelleher |
| Title | Dynamic model routing and cascading for efficient LLM inference: A survey | Dynamic Model Routing and Cascading for Efficient LLM Inference: A Survey |
| Venue | arXiv preprint arXiv:2603.04445, 2026 | arXiv:2603.04445, Feb 2026 |
| Source | [arXiv:2603.04445](https://arxiv.org/abs/2603.04445) |

**Status: SUSPECT.** The paper itself is REAL and the arXiv ID and year are correct. However, the bibitem uses the **placeholder** "Survey Authors" instead of actual author names. This is unacceptable for submission.

**MUST FIX:** Replace "Survey Authors" with actual author names:
```
Y.~Moslem and J.~D.~Kelleher.
```

---

### 11. `hong2023metagpt` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | S. Hong, M. Zhuge, et al. | Sirui Hong, Mingchen Zhuge, +13 authors |
| Title | MetaGPT: Meta programming for a multi-agent collaborative framework | MetaGPT: Meta Programming for A Multi-Agent Collaborative Framework |
| Venue | ICLR, 2024 | ICLR 2024 (Oral, top 1.2%) |
| Source | [ICLR 2024 oral](https://iclr.cc/virtual/2024/oral/19756), [arXiv:2308.00352](https://arxiv.org/abs/2308.00352) |

**Status: VERIFIED.** All metadata matches. Citation key uses 2023 (arXiv date) while venue is ICLR 2024 -- this is acceptable and common practice.

---

### 12. `hu2024routerbench` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | Q. J. Hu, J. Bieker, X. Li, and N. Jiang | Qitian Jason Hu, Jacob Bieker, Xiuyu Li, Nan Jiang, +4 more |
| Title | RouterBench: A benchmark for multi-LLM routing system | RouterBench: A Benchmark for Multi-LLM Routing System |
| Venue | arXiv preprint arXiv:2403.12031, 2024 | arXiv:2403.12031, Mar 2024 |
| Source | [arXiv:2403.12031](https://arxiv.org/abs/2403.12031) |

**Status: VERIFIED.** All metadata matches. The bibitem lists 4 of 8 authors, which is acceptable.

---

### 13. `jin2008pareto_ml` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | Y. Jin and B. Sendhoff | Yaochu Jin and Bernhard Sendhoff |
| Title | Pareto-based multiobjective machine learning: An overview and case studies | Pareto-Based Multiobjective Machine Learning: An Overview and Case Studies |
| Venue | IEEE Trans. Systems, Man, and Cybernetics, Part C, 2008 | IEEE Trans. Systems, Man, and Cybernetics, Part C, 38(3), 2008 |
| Source | [IEEE Xplore](https://ieeexplore.ieee.org/document/4492360/) |

**Status: VERIFIED.** All metadata matches.

---

### 14. `liu2024optllm` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | Y. Liu, H. Zhang, Y. Miao, V.-H. Le, and Z. Li | Yueyue Liu, Hongyu Zhang, Yuantian Miao, Van-Hoang Le, Zhiqiang Li |
| Title | OptLLM: Optimal assignment of queries to large language models | OptLLM: Optimal Assignment of Queries to Large Language Models |
| Venue | ICWS, 2024 | ICWS 2024 (IEEE International Conference on Web Services) |
| Source | [arXiv:2405.15130](https://arxiv.org/abs/2405.15130), [IEEE Xplore](https://ieeexplore.ieee.org/document/10707591/) |

**Status: VERIFIED.** All metadata matches.

---

### 15. `marinov2021pareto_frontier` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | T. V. Marinov and J. Zimmert | Teodor V. Marinov and Julian Zimmert |
| Title | The Pareto frontier of model selection for general contextual bandits | The Pareto Frontier of Model Selection for General Contextual Bandits |
| Venue | arXiv preprint arXiv:2110.13282, 2021 | arXiv:2110.13282, Oct 2021 (published NeurIPS 2021) |
| Source | [arXiv:2110.13282](https://arxiv.org/abs/2110.13282), [NeurIPS 2021 proceedings](https://proceedings.neurips.cc/paper/2021/hash/9570efef719d705326f0ff817ef084e6-Abstract.html) |

**Status: VERIFIED.** All metadata matches. Note: the paper was published at NeurIPS 2021, so the venue could be updated from "arXiv preprint" to "NeurIPS, 2021" for a stronger reference.

---

### 16. `mialon2023gaia` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | G. Mialon, C. Fourrier, C. Swift, T. Wolf, Y. LeCun, and T. Scialom | Gregoire Mialon, Clementine Fourrier, Craig Swift, Thomas Wolf, Yann LeCun, Thomas Scialom |
| Title | GAIA: A benchmark for general AI assistants | GAIA: a benchmark for General AI Assistants |
| Venue | arXiv preprint arXiv:2311.12983, 2023 | arXiv:2311.12983, Nov 2023 |
| Source | [arXiv:2311.12983](https://arxiv.org/abs/2311.12983) |

**Status: VERIFIED.** All metadata matches.

---

### 17. `ong2024routellm` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | I. Ong, A. Almahairi, V. Wu, W.-L. Chiang, T. Wu, J. E. Gonzalez, M. W. Kadous, and I. Stoica | Isaac Ong, Amjad Almahairi, Vincent Wu, Wei-Lin Chiang, Tianhao Wu, Joseph E. Gonzalez, M Waleed Kadous, Ion Stoica |
| Title | RouteLLM: Learning to route LLMs with preference data | RouteLLM: Learning to Route LLMs with Preference Data |
| Venue | ICLR, 2025 | ICLR 2025 |
| Source | [ICLR 2025 proceedings](https://proceedings.iclr.cc/paper_files/paper/2025/hash/5503a7c69d48a2f86fc00b3dc09de686-Abstract-Conference.html), [arXiv:2406.18665](https://arxiv.org/abs/2406.18665) |

**Status: VERIFIED.** All metadata matches.

---

### 18. `qian2023chatdev` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | C. Qian, W. Liu, et al. | Chen Qian, Wei Liu, +12 authors |
| Title | ChatDev: Communicative agents for software development | ChatDev: Communicative Agents for Software Development |
| Venue | ACL, 2024 | ACL 2024 (62nd Annual Meeting of the ACL, Long Papers) |
| Source | [ACL Anthology](https://aclanthology.org/2024.acl-long.810/), [arXiv:2307.07924](https://arxiv.org/abs/2307.07924) |

**Status: VERIFIED.** All metadata matches.

---

### 19. `simon1962complexity` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | H. A. Simon | Herbert A. Simon |
| Title | The architecture of complexity | The Architecture of Complexity |
| Venue | Proc. American Philosophical Society, 106:467-482, 1962 | Proceedings of the American Philosophical Society, 106:467-482, 1962 |
| Source | [JSTOR](https://www.jstor.org/stable/i240648), [multiple academic repositories](https://faculty.sites.iastate.edu/tesfatsi/archive/tesfatsi/ArchitectureOfComplexity.HSimon1962.pdf) |

**Status: VERIFIED.** All metadata matches exactly.

---

### 20. `su2025daao` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | J. Su, Q. Lan, Y. Xia, et al. | Jinwei Su, Qizhen Lan, Yinghui Xia, +6 more |
| Title | Difficulty-aware agent orchestration in LLM-powered workflows | Difficulty-Aware Agent Orchestration in LLM-Powered Workflows (v2 title) |
| Venue | arXiv preprint arXiv:2509.11079, 2025 | arXiv:2509.11079, Sep 2025 |
| Source | [arXiv:2509.11079](https://arxiv.org/abs/2509.11079) |

**Status: VERIFIED.** All metadata matches. Note: the paper title changed across versions -- v1/v2 used "Difficulty-Aware Agent Orchestration in LLM-Powered Workflows" (matching the bibitem), while the latest version (v5) uses "Difficulty-Aware Agentic Orchestration for Query-Specific Multi-Agent Workflows". The bibitem matches the v2 title, which is acceptable.

---

### 21. `treacle2024` -- SUSPECT

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | TREACLE Authors | Xuechen Zhang, Zijian Huang, Ege Onur Taga, Carlee Joe-Wong, Samet Oymak, Jiasi Chen |
| Title | Efficient contextual LLM cascades through budget-constrained policy learning | Efficient Contextual LLM Cascades through Budget-Constrained Policy Learning |
| Venue | NeurIPS, 2024 | NeurIPS 2024 |
| Source | [NeurIPS 2024 poster](https://neurips.cc/virtual/2024/poster/94574), [arXiv:2404.13082](https://arxiv.org/abs/2404.13082) |

**Status: SUSPECT.** The paper itself is REAL and the venue/year are correct. However, the bibitem uses the **placeholder** "TREACLE Authors" instead of actual author names. This is unacceptable for submission.

**MUST FIX:** Replace "TREACLE Authors" with actual author names:
```
X.~Zhang, Z.~Huang, E.~O.~Taga, C.~Joe-Wong, S.~Oymak, and J.~Chen.
```

---

### 22. `wang2024cascade_aware` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | C. Wang, S. Augenstein, and K. Rush | Congchao Wang, Sean Augenstein, Keith Rush, +5 more |
| Title | Cascade-aware training of language models | Cascade-Aware Training of Language Models |
| Venue | arXiv preprint arXiv:2406.00060, 2024 | arXiv:2406.00060, May 2024 |
| Source | [arXiv:2406.00060](https://arxiv.org/abs/2406.00060) |

**Status: VERIFIED.** All metadata matches. The bibitem lists 3 of 8 authors, which is acceptable.

---

### 23. `wu2023autogen` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | Q. Wu, G. Bansal, J. Zhang, et al. | Qingyun Wu, Gagan Bansal, Jieyu Zhang, +11 more |
| Title | AutoGen: Enabling next-gen LLM applications via multi-agent conversation | AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation |
| Venue | arXiv preprint arXiv:2308.08155, 2023 | arXiv:2308.08155, Aug 2023 |
| Source | [arXiv:2308.08155](https://arxiv.org/abs/2308.08155) |

**Status: VERIFIED.** All metadata matches.

---

### 24. `zhang2023ecoassistant` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | J. Zhang, R. Krishna, A. H. Awadallah, and C. Wang | Jieyu Zhang, Ranjay Krishna, Ahmed H. Awadallah, Chi Wang |
| Title | EcoAssistant: Using LLM assistant more affordably and accurately | EcoAssistant: Using LLM Assistant More Affordably and Accurately |
| Venue | arXiv preprint arXiv:2310.03046, 2023 | arXiv:2310.03046, Oct 2023 |
| Source | [arXiv:2310.03046](https://arxiv.org/abs/2310.03046) |

**Status: VERIFIED.** All metadata matches.

---

### 25. `zhu2022pareto_bandits` -- VERIFIED

| Field | Bibitem | Verified |
|-------|---------|----------|
| Authors | Y. Zhu and R. Nowak | Yinglun Zhu and Robert Nowak |
| Title | Pareto optimal model selection in linear bandits | Pareto Optimal Model Selection in Linear Bandits |
| Venue | AISTATS, 2022 | AISTATS 2022 (Proceedings of the 25th AISTATS) |
| Source | [AISTATS 2022 proceedings](https://proceedings.mlr.press/v151/zhu22g.html), [arXiv:2102.06593](https://arxiv.org/abs/2102.06593) |

**Status: VERIFIED.** All metadata matches.

---

## Issues Requiring Action

### CRITICAL (Must Fix Before Submission)

1. **`c3po2025`**: Uses placeholder "C3PO Authors" instead of real author names (Valkanas, Pal, Rumiantsev, Zhang, Coates).
2. **`treacle2024`**: Uses placeholder "TREACLE Authors" instead of real author names (Zhang, Huang, Taga, Joe-Wong, Oymak, Chen).
3. **`dynamic_routing_survey2026`**: Uses placeholder "Survey Authors" instead of real author names (Moslem, Kelleher).

### RECOMMENDED (Improve Quality)

4. **`aggarwal2024automix`**: The "S. P. Potharaju" abbreviation is nonstandard for "Srividya Pranavi Potharaju". Consider fixing.
5. **`du2023debate`**: Citation key says 2023 but paper was published at ICML 2024. The bibitem body is correct -- this is cosmetic only.
6. **`marinov2021pareto_frontier`**: Listed as arXiv preprint but was published at NeurIPS 2021. Could upgrade venue.
7. **`dang2025evolving`**: Listed as arXiv preprint but was accepted at NeurIPS 2025. Could upgrade venue.
8. **`dekoninck2024unified`**: Listed as arXiv preprint but was accepted at ICML 2025. Could upgrade venue.

### NO ACTION NEEDED

- All 25 papers are REAL (zero fabrications detected).
- 18 citations have fully correct metadata with no issues.
- No wrong-paper attributions detected.
- No significant year errors detected.
- No venue misattributions detected.

---

## Verification Methodology

Each citation was searched across:
1. **arXiv** -- for preprints and paper existence
2. **Conference proceedings** (NeurIPS, ICML, ICLR, ACL, AISTATS, IEEE) -- for published versions
3. **Semantic Scholar** -- for cross-referencing authors and metadata
4. **Google Scholar / Web** -- for additional confirmation

For each paper, the following fields were independently verified:
- Paper title (exact match)
- Author names (first author match + spot-check co-authors)
- Publication year
- Venue name
- arXiv ID (where applicable)
