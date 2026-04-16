# AI Writing Pattern Audit Report

**Paper:** ParetOrch: Pareto-Optimal Topology Routing
**Scanned sections:** abstract, introduction, related_work, method, experiments, results, discussion, conclusion
**Audit date:** 2026-03-17
**Auditor:** Automated pattern scanner (9 pattern categories)

---

## Pattern Scan Results

### 1. Hedging Chains
**Scanned for:** "it is worth noting that", "it should be mentioned", "it is important to note", "arguably", "to some extent"
**Result:** CLEAN -- zero occurrences across all sections.

---

### 2. Filler Openings
**Scanned for:** "In recent years", "A growing body of research", "In the realm of"
**Result:** CLEAN -- zero occurrences across all sections.

---

### 3. Overused AI Vocabulary
**Scanned for:** delve, comprehensive, landscape, paradigm shift, leverage, facilitate, harness, cutting-edge, state-of-the-art, novel (overuse), robust (overuse)

| Word | Occurrences | Verdict |
|------|-------------|---------|
| delve | 0 | Clean |
| comprehensive | 0 | Clean |
| landscape | 0 | Clean |
| paradigm shift | 0 | Clean |
| leverage | 0 | Clean |
| facilitate | 0 | Clean |
| harness | 0 | Clean |
| cutting-edge | 0 | Clean |
| novel | 0 | Clean |
| state-of-the-art | 1 | Exempt (benchmark comparison) |
| robust | 3 | Legitimate (statistical terminology) |

**Details:**
- **state-of-the-art** (related_work L24): Used to describe DAAO's benchmark claims -- "achieving state-of-the-art results on six benchmarks." This is a literal benchmark comparison, which is the exempted context. No flag.
- **robust** (introduction L34, results L10, results L78): All three refer to "statistical robustness checks" -- a standard methodological term, not AI filler. No flag.

**Result:** CLEAN -- no flaggable AI vocabulary.

---

### 4. Mechanical Transitions
**Scanned for:** Excessive repetition of "Furthermore", "Moreover", "Additionally"
**Result:** CLEAN -- zero occurrences of any of these three transitions across all sections.

---

### 5. Em Dash Overuse (threshold: >3 per section)

| Section | Count | Verdict |
|---------|-------|---------|
| abstract | 3 | OK (at threshold) |
| introduction | 7 | **FLAG** |
| related_work | 6 | **FLAG** |
| method | 3 | OK (at threshold) |
| experiments | 0 | Clean |
| results | 5 | **FLAG** |
| discussion | 7 | **FLAG** |
| conclusion | 3 | OK (at threshold) |

#### FLAG 5a: introduction (7 em dashes)
- L10: "...quality on different tasks---and each carries a radically different price tag."
- L12: "...always-flat costs \$135---a \$245 daily gap"
- L12: "...debate reaches 61.1\%---a 25.5 percentage-point gap"
- L14: "...error-correction capacity---dimensions that model selection alone cannot capture"
- L16: "...flat topology is always Pareto-optimal---no amount of coordination overhead improves..."
- L20: "...from \$0.047 to \$0.023---a practitioner-relevant metric"
- L32: "...flat topology is always Pareto-optimal---a simple rule of thumb"

**Suggested fix:** Replace 3-4 em dashes with periods, colons, or parentheses. For example:
- "...always-flat costs \$135---a \$245 daily gap" -> "...always-flat costs \$135, a \$245 daily gap"
- "...from \$0.047 to \$0.023---a practitioner-relevant metric" -> "...from \$0.047 to \$0.023 (a practitioner-relevant metric...)"

#### FLAG 5b: related_work (6 em dashes)
- L14: "...three complementary strategies---prompt adaptation, LLM approximation, and LLM cascading---achieving up to 98\%..."
- L18: "Topology choice---flat versus hierarchical versus debate---determines both cost structure..."
- L26: "...as a function of task characteristics---precisely the gap ParetOrch fills."
- L32: "...effective for LLM cost-quality tradeoffs---but all operate at the model or inference level."

**Suggested fix:** Replace 2-3 em dashes with colons or commas. For example:
- "...task characteristics---precisely the gap ParetOrch fills" -> "...task characteristics, precisely the gap ParetOrch fills"

#### FLAG 5c: results (5 em dashes)
- L24: "...from \$0.047 to \$0.023---a 51.1% reduction"
- L41: "...reaches 17.6 percentage points---large enough to justify"
- L76: "...distinct from quality optimization---selecting randomly among..."
- L80: "The main finding---53.3% cost reduction at 95.6% quality retention---holds across all three seeds"

**Suggested fix:** Convert 2 em dashes to colons or parenthetical asides. The paired em dash on L80 is fine stylistically; target the standalone ones.

#### FLAG 5d: discussion (7 em dashes)
- L16: "...a single knob---the quality threshold \$\\tau\$---to navigate the cost-quality frontier"
- L16: "...which coordination pattern?\"---and the two decisions compose"
- L24: "...the crossover point moves upward---stronger models make flat topology competitive"
- L30: "...captures the dominant component---token volume scaled by per-token price---but may underestimate"
- L38: "...distributions may cluster differently---for example, production coding assistants..."

**Suggested fix:** Replace 3-4 em dashes. Particularly the double em dash constructions on L16 and L30, which are the heaviest AI fingerprint. For example:
- "...a single knob---the quality threshold---to navigate" -> "...a single knob (the quality threshold \$\\tau\$) to navigate"
- "...captures the dominant component---token volume...---but may" -> "...captures the dominant component (token volume...) but may"

---

### 6. Sentence Length Uniformity

| Section | Mean (words) | Std Dev | CV | Verdict |
|---------|-------------|---------|-----|---------|
| abstract | 19.9 | 8.8 | 0.44 | OK |
| introduction | 14.0 | 7.1 | 0.51 | OK |
| related_work | 17.1 | 8.8 | 0.51 | OK |
| method | 19.2 | 10.2 | 0.53 | OK |
| experiments | 20.1 | 9.4 | 0.47 | OK |
| results | 17.0 | 7.8 | 0.46 | OK |
| discussion | 17.1 | 7.0 | 0.41 | OK |
| conclusion | 21.2 | 10.2 | 0.48 | OK |

All sections show coefficient of variation (CV) above 0.40, indicating healthy sentence length variety. No uniformity flags.

**Result:** CLEAN -- good variance in all sections.

---

### 7. Boilerplate Conclusions
**Scanned for:** "In conclusion", "To summarize", "In summary"
**Result:** CLEAN -- zero occurrences. The conclusion opens with "ParetOrch reframes multi-agent cost optimization as a topology selection problem." Direct and specific.

---

### 8. Significance Inflation
**Scanned for:** "groundbreaking", "revolutionary", "transformative"
**Result:** CLEAN -- zero occurrences across all sections.

---

### 9. Passive Voice Clusters (3+ consecutive passive sentences)
**Result:** CLEAN -- no clusters of 3 or more consecutive passive-voice sentences detected in any section.

---

## Flag Summary

| # | Section | Pattern | Severity |
|---|---------|---------|----------|
| 1 | introduction | Em dash overuse (7, threshold 3) | Moderate |
| 2 | related_work | Em dash overuse (6, threshold 3) | Moderate |
| 3 | results | Em dash overuse (5, threshold 3) | Minor |
| 4 | discussion | Em dash overuse (7, threshold 3) | Moderate |

**Total flags:** 4
**Distinct pattern types flagged:** 1 (em dash overuse only)
**Severe patterns:** 0

---

## Verdict: CAUTION

**Score: 4 flags (all em dash overuse)**

The paper is clean on 8 of 9 AI pattern categories. No hedging chains, no filler openings, no AI vocabulary, no mechanical transitions, good sentence variety, no boilerplate conclusions, no significance inflation, and no passive voice clusters. This is unusually clean writing.

The single recurring issue is em dash overuse, present in 4 of 8 sections. Em dashes are a well-documented AI writing fingerprint when used as a crutch for appositives and parenthetical asides. The paper uses a total of ~34 em dashes across body text, with concentrations in introduction (7), related_work (6), discussion (7), and results (5). The most flaggable pattern is the "X---a Y" construction (value followed by em dash followed by restatement), which appears at least 8 times across the paper.

**Recommended action:** Reduce total em dash count by ~40% (remove 12-14 instances). Prioritize replacing:
1. The "number---a description" pattern (e.g., "\$0.023---a 51.1% reduction") with parentheses or commas
2. Double em dash parentheticals (e.g., "knob---the quality threshold---to") with actual parentheses
3. Terminal em dashes before clauses (e.g., "characteristics---precisely the gap") with colons or periods

After these edits, the paper should pass with 0-2 minor flags.
