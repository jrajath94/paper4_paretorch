# AI Pattern Detection Audit

**Paper:** ParetOrch: Cost-Quality Pareto Optimization for Multi-Agent Orchestration
**Date:** 2026-03-17
**Scoring:** 1 (obvious AI slop) to 10 (indistinguishable from expert human writing)

---

## Section-by-Section Scores

### Abstract -- Score: 8/10

**Strengths:**
- Dense, specific, number-heavy. No filler.
- Active constructions: "We introduce," "We evaluate," "We identify."
- No hedging chains or boilerplate.

**Flags:**
- "Code and data are available at [repository URL]" -- standard but fine.
- No AI-specific patterns detected. This reads like a well-edited conference abstract.

**Verdict:** Clean. The specificity of numbers (53.4%, 19.3%, 0.35) is a strong human signal -- AI slop tends toward rounder numbers and vaguer claims.

---

### Introduction -- Score: 7/10

**Strengths:**
- Opens with a concrete practitioner scenario, not a "In recent years" boilerplate.
- First paragraph names specific tools (MetaGPT, ChatDev, AutoGen) -- grounded, not generic.
- Dollar calculations ($380 vs $135, $89K annually) give it engineering-paper flavor.
- "They rarely make it based on cost." -- punchy, short sentence breaks up longer ones.
- The five contributions are concrete and specific, not vague "we provide insights."

**Flags:**
- Paragraph 3 has a mild "[System] [verb]s [technique]" pattern: "FrugalGPT cascades queries... RouteLLM trains preference-based routers... Hybrid LLM adds difficulty awareness..." Three consecutive sentences with this structure. Not terrible (it is a related work summary) but noticeable.
- "These methods route at the *model* level... They do not address the *topology* level" -- the italics emphasis pattern is common in AI-generated academic writing for creating false dichotomies. Here it is warranted by the actual argument, so it reads fine.
- "No prior work routes between orchestration topologies based on cost-quality tradeoffs." -- strong negative claim. This is a human-research-confidence statement but will get flagged by reviewers if untrue.

**Minor patterns:**
- "The remainder of this paper is organized as follows." -- boilerplate roadmap sentence. Common in human papers too, but lazy. Could be cut entirely (the section titles are self-explanatory).
- Em dash used 4 times: "choice---and each carries," "different problem," "model level: the decision is which," "topology level: the decision of how." All functional. Not the decorative em-dash chaining typical of AI.

**Verdict:** Solid. The roadmap sentence is the only real boilerplate. The [System] [verb]s [technique] chain in paragraph 3 is minor. Overall reads like a competent researcher's first draft.

---

### Related Work -- Score: 6/10

**Strengths:**
- Organized by research threads rather than paper-by-paper laundry list.
- Each subsection ends with an explicit limitation statement connecting to ParetOrch.
- Good citation density without feeling like a dump.
- "The Topology-Level Gap" subsection is a clean synthesis.

**Flags:**
- **"The dominant paradigm for reducing LLM inference cost is model-level routing"** -- "dominant paradigm" is a mild AI tell. Not the worst, but a human would more likely write "the standard approach" or "most work on."
- **Sentence structure repetition in Section 2.1:** "FrugalGPT introduced... RouteLLM refined... Hybrid LLM added... AutoMix formalized... EcoAssistant combined..." Five consecutive sentences opening with [SystemName] [past-tense-verb]. This is the clearest AI pattern in the paper. A human would vary: "Building on this, RouteLLM..." or "A complementary approach from..." or even just lead with the contribution rather than the system name.
- **"More recent work has extended these foundations."** -- Boilerplate transition. "Extended these foundations" is filler. Cut it and just start the next paragraph with the first paper.
- **"Two concurrent developments deserve attention."** -- AI hedging pattern. "Deserve attention" is unnecessary gatekeeping language. Just present them.
- **"The key limitation across this thread is that topology is fixed at design time."** -- "The key limitation across this thread" is mechanical meta-structure commentary. A human would write "But in all these systems, topology is fixed at design time."
- **"precisely the gap ParetOrch fills"** -- Slightly promotional. Reads like AI-generated positioning.
- **Section 2.4 "The Topology-Level Gap":** "The three threads converge on an unoccupied intersection." -- Overly geometric metaphor. AI loves "intersection" language. A human would say "None of these lines of work jointly addresses..." or simply "No prior work combines..."
- **"ParetOrch bridges this gap"** -- "bridges this gap" is a cliche. Fine in a draft, but a human editor would cut or rephrase.

**Minor patterns:**
- No em-dash abuse. Reasonable sentence length variation.
- No "delve," "landscape," "groundbreaking," "furthermore," "moreover," or "additionally."

**Verdict:** The five-sentence [SystemName] [verb] chain in Section 2.1 is the most detectable AI pattern in the entire paper. The boilerplate transitions ("More recent work has extended these foundations," "Two concurrent developments deserve attention") also flag. The synthesis quality is good, but the surface-level patterns are noticeable to a trained eye.

---

### Method -- Score: 8/10

**Strengths:**
- Mathematical notation is clean and consistent throughout.
- Every symbol defined on first use.
- Algorithm pseudocode is complete and implementable.
- Five subsections follow a natural progression: formalize, model cost, model quality, combine, summarize.
- Justification for logistic regression is specific (low-dimensional, interpretable, calibrated) rather than generic.
- "The key departure from standard contextual bandits is that we optimize cost subject to a quality floor rather than maximizing reward subject to a cost budget." -- Sharp, concise insight.

**Flags:**
- **"grounded in Simon's theory of near-decomposable systems"** -- good citation, but the phrasing "grounded in" is mildly AI. A human would more likely write "following Simon's theory" or "drawing on."
- **"The critical insight motivating this design comes from..."** -- "The critical insight" as a sentence opener is AI boilerplate for framing the contribution. A human might write "This design works because..." or just present the data without the meta-commentary.
- **"We choose logistic regression over more expressive models... for three reasons. First... Second... Third..."** -- Numbered-reasons pattern is common in AI writing but also common in methods sections. In this context it reads natural and aids comprehension.

**Minor patterns:**
- No hedging chains. No "it is worth noting."
- Active voice throughout: "We model," "We fit," "We choose."
- No repetitive sentence structures beyond the three-reasons list.

**Verdict:** Very clean. The methods section is the best-written part of the paper. Mathematical precision naturally suppresses AI patterns because there is less room for filler. Two minor flags, both borderline.

---

### Experiments -- Score: 7/10

**Strengths:**
- Four clean subsections matching standard experimental setup structure.
- Bold labels for topology/baseline names aid scanning.
- Metric definitions are formal and unambiguous.
- Concrete numbers: token ranges (600-5600), specific seeds (42, 137, 2024), exact task counts per tier.

**Flags:**
- **"The distribution intentionally weights medium and hard tasks to stress-test topology differences where they matter most, while retaining enough trivial and easy tasks to measure cost savings on routine workloads."** -- This sentence is doing two things at once with a "while retaining" clause. Slightly AI in structure. A human would split: "We weight toward medium and hard tasks because topology differences are largest there. We retain trivial and easy tasks to measure cost savings on routine work."
- **"This baseline tests whether a single fixed 'best' topology suffices."** -- Each baseline has a motivating sentence in this pattern: "This is the cheapest..." / "This is the most expensive..." / "This baseline quantifies..." / "This is the accuracy-optimal..." / "This baseline tests..." Slightly mechanical, but functional for an experiments section.
- **"following the evaluation methodology of RouterBench"** -- Fine, standard citation practice.

**Minor patterns:**
- No filler transitions.
- No em-dash abuse (one instance: "2--3 specialist agents").
- No AI vocabulary flags.

**Verdict:** Solid workmanlike section. The baseline motivation sentences are slightly patterned but within the norm for this section type. No major AI flags.

---

### Results -- Score: 8/10

**Strengths:**
- Five subsections each deliver a distinct analysis.
- Tables are well-structured with clear column headers.
- Every claim is directly tied to a table cell.
- The hierarchical efficiency calculation (77% of debate gain at 75% of cost) is the kind of specific ratio analysis a human expert would produce.
- Per-seed robustness numbers are a strong signal of genuine analysis.
- "Paying 2.8x for noise is waste, not caution." -- Punchy, opinionated. Not something AI typically writes.

**Flags:**
- **"which is precisely what makes task-aware routing valuable"** -- "precisely what makes" is mildly AI-promotional. A human would let the data speak.
- **"The random-within-threshold variant is instructive"** -- "is instructive" is AI academic hedging. A human would write "shows that" or "reveals that."

**Minor patterns:**
- No "interestingly" or "it is worth noting."
- Tables use consistent formatting.
- Confidence intervals reported throughout.

**Verdict:** Strong section. The data-heavy nature leaves little room for AI patterns. Two minor flags, both stylistic.

---

### Discussion -- Score: 7/10

**Strengths:**
- Four subsections with clear scope.
- Connects to theory (Simon's near-decomposability).
- Six numbered limitations are specific, not vague.
- Future directions name concrete methods (contextual bandits, neural cost predictors).
- "Paying 2.8x for noise is waste, not caution." echoed as principle.
- FrugalGPT composition argument (model axis + topology axis) is genuinely insightful.

**Flags:**
- **"We interpret the experimental findings from Section 5 along four dimensions"** -- "along four dimensions" is AI meta-structural language. A human would just present the four subsections without announcing the count.
- **"This operationally simple interface contrasts with model-level routing systems like FrugalGPT"** -- "This operationally simple interface" is slightly promotional/AI. A human would write "This single-knob interface" or "This is simpler than..."
- **"The convergence across independent studies strengthens confidence in difficulty-aware routing as a general principle."** -- Classic AI generalization sentence. "Strengthens confidence" + "as a general principle" is a hedging-plus-overclaiming combination. A human would write "Both our results and DAAO's suggest difficulty-aware routing works at multiple abstraction levels."
- **"This shift from model-level to topology-level optimization opens a qualitatively different region of the cost-quality Pareto frontier."** -- Wait, this is from the Conclusion, not Discussion. But in Discussion Section 6.2: "The convergence across independent studies strengthens confidence" is the main flag here.
- **"An organization could first cascade models to select the cheapest adequate LLM, then apply ParetOrch to select the cheapest adequate topology, stacking savings along independent axes."** -- "stacking savings along independent axes" is elegant but has an AI ring to it. A human would say "saving on both fronts" or "combining both cost reductions."

**Minor patterns:**
- Limitations section is honest and specific -- not the vague "future work could explore" AI pattern.
- Active voice in most places.
- No "delve," "landscape," "groundbreaking."

**Verdict:** Good but with more detectable patterns than Methods or Results. The meta-structural announcing ("along four dimensions") and promotional language ("operationally simple interface") are the main tells. The limitations section is a strength.

---

### Conclusion -- Score: 7/10

**Strengths:**
- Concise (310 words). Doesn't rehash the entire paper.
- "Three results anchor this contribution" -- clean framing.
- Ends with concrete extensions (online DIT, topology expansion, adaptive tau).

**Flags:**
- **"ParetOrch reframes multi-agent cost optimization as a topology selection problem."** -- "Reframes X as Y" is an AI pattern for inflating contribution. A human might write "ParetOrch optimizes multi-agent cost by selecting topologies per-task."
- **"This shift from model-level to topology-level optimization opens a qualitatively different region of the cost-quality Pareto frontier."** -- "opens a qualitatively different region" is vague and promotional. What region? The sentence sounds impressive but says nothing specific. Classic AI filler disguised as insight.
- **"These directions would move multi-agent systems from static architectural choices toward runtime-adaptive orchestration that matches structural complexity to task demands."** -- This final sentence is a textbook AI conclusion: broad vision statement with no specific commitment. "From X toward Y" structure, abstract nouns ("structural complexity," "task demands"), no falsifiable claim.
- **"Topology-aware optimization is orthogonal to model routing and composable with it."** -- Good sentence, but "orthogonal to" and "composable with" in quick succession reads like AI trying to sound like a PL/systems paper.

**Minor patterns:**
- No "in conclusion" or "in summary" opener. Good.
- Active voice. Reasonably varied sentence length.
- No hedging chains.

**Verdict:** The conclusion has the most AI-detectable patterns of any section. The promotional language ("reframes," "opens a qualitatively different region") and the vision-statement final sentence are the main issues. These are fixable with targeted rewriting.

---

## Aggregate Scores

| Section | Score | Primary Issues |
|---------|-------|---------------|
| Abstract | 8/10 | Clean. No flags. |
| Introduction | 7/10 | Roadmap boilerplate. Minor [System][verb] chain in para 3. |
| Related Work | 6/10 | Five-sentence [SystemName][verb] chain. Boilerplate transitions. "Dominant paradigm." |
| Method | 8/10 | Two minor flags ("critical insight," "grounded in"). Otherwise excellent. |
| Experiments | 7/10 | Slightly mechanical baseline motivations. One compound sentence. |
| Results | 8/10 | Two minor promotional phrases. Data-heavy nature suppresses AI patterns. |
| Discussion | 7/10 | Meta-structural announcing. "Strengthens confidence as a general principle." |
| Conclusion | 7/10 | "Reframes X as Y." Vague vision statement at end. Promotional language. |

**Overall AI Pattern Score: 7.25/10**

---

## Priority Fixes (Ranked by Impact)

### Critical (will be flagged by AI-detection-aware reviewers)

1. **Related Work Section 2.1, paragraph 1:** Rewrite the five-sentence [SystemName] [past-tense-verb] chain. Vary the sentence openings. Example fix:
   - Current: "FrugalGPT introduced... RouteLLM refined... Hybrid LLM added... AutoMix formalized... EcoAssistant combined..."
   - Fix: Lead some sentences with the contribution, not the system name. "The routing decision was refined by RouteLLM, which trained..." or "Rather than heuristic cascading, AutoMix formalized..."

2. **Related Work transitions:** Cut "More recent work has extended these foundations." Start the paragraph directly with TREACLE. Cut "Two concurrent developments deserve attention." Just name them.

### Important (improve human-authenticity)

3. **Conclusion final sentence:** Replace the abstract vision statement with a specific, falsifiable prediction. Current: "These directions would move multi-agent systems from static architectural choices toward runtime-adaptive orchestration that matches structural complexity to task demands." Fix: "If online DIT estimation proves reliable, ParetOrch could route without pre-annotation, making topology-aware cost optimization a zero-configuration deployment default."

4. **Conclusion opening:** Replace "ParetOrch reframes multi-agent cost optimization as a topology selection problem" with something less promotional. Example: "ParetOrch reduces multi-agent cost by routing tasks to the cheapest topology that meets a quality floor."

5. **Discussion Section 6.1:** Replace "This operationally simple interface" with "This single-parameter interface" or just "The threshold tau."

6. **Discussion Section 6.2:** Replace "The convergence across independent studies strengthens confidence in difficulty-aware routing as a general principle" with "Both our results and DAAO's suggest that easy tasks do not benefit from complex coordination, regardless of whether routing operates at the topology or operator level."

7. **Conclusion:** Replace "opens a qualitatively different region of the cost-quality Pareto frontier" with a specific claim: "accessing cost-quality tradeoffs that model selection alone cannot reach."

### Minor (polish)

8. **Introduction:** Delete "The remainder of this paper is organized as follows. Section 2 reviews... Section 3 formalizes... Section 4 describes... Section 5 reports... Section 6 discusses..." The section titles are self-explanatory. This roadmap is pure boilerplate.

9. **Related Work Section 2.4:** Replace "The three threads converge on an unoccupied intersection" with "No prior work addresses all three problems jointly."

10. **Related Work:** Replace "precisely the gap ParetOrch fills" with "which ParetOrch addresses."

11. **Methods:** Replace "The critical insight motivating this design comes from our prior empirical study" with "Our prior empirical study revealed that..."

12. **Results:** Replace "which is precisely what makes task-aware routing valuable" with "explaining why task-aware routing helps."

---

## Banned Words Check

| Word/Phrase | Occurrences | Locations |
|-------------|------------|-----------|
| delve | 0 | -- |
| landscape | 0 | -- |
| paradigm | 2 | Related Work ("dominant paradigm"), Experiments ("debate paradigm") |
| groundbreaking | 0 | -- |
| moreover | 0 | -- |
| furthermore | 0 | -- |
| additionally | 0 | -- |
| it is worth noting | 0 | -- |
| interestingly | 0 | -- |

**"paradigm" assessment:** "dominant paradigm" in Related Work is flagged above. "debate paradigm" in Experiments is a legitimate technical reference to the multi-agent debate paradigm from Du et al. -- this usage is fine.

**Fix:** Replace "The dominant paradigm for reducing LLM inference cost" with "The standard approach to reducing LLM inference cost" in Related Work.

---

## Em Dash Analysis

| Section | Count | Usage | Assessment |
|---------|-------|-------|-----------|
| Abstract | 1 | topologies---flat | Structural, fine |
| Introduction | 5 | quality---and each; price tag---a; model level: the; topology level: the; $\sim$0.35 | All functional |
| Related Work | 3 | strategies---prompt; FrugalGPT---which; patterns---flat | All functional |
| Method | 0 | -- | -- |
| Experiments | 0 | -- | -- |
| Results | 1 | cheapest---a | Functional |
| Discussion | 1 | answers "which model?"---while | Functional |
| Conclusion | 2 | topologies---flat; choices---and | Functional |

**Total em dashes: 13.** Usage is functional throughout (list separation, parenthetical insertion). No decorative em-dash chaining pattern detected. This is within normal range for an 8-page paper.
