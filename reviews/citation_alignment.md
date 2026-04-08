# Citation Alignment Audit

**File:** `paper/output/main.tex`
**Date:** 2026-03-17
**Verdict:** PASS -- all checks clean

---

## 1. Orphan Cites (cite key with no matching bibitem)

**None found.** All 25 `\cite{}` keys in the body resolve to a `\bibitem{}`.

## 2. Uncited Bibitems (dead references)

**None found.** All 25 `\bibitem{}` entries are cited at least once in the body.

## 3. Citation Ordering

Bibitems are in **strict alphabetical order** by key (agrawal... through zhu...). No reordering needed.

## 4. Double Citations

No duplicate citations within the same sentence or paragraph. Multi-cites (e.g., `\cite{chen2023frugalgpt, ong2024routellm}` on line 293) reference distinct works. Repeated cites across sections (e.g., `chen2023frugalgpt` in Introduction, Related Work, and Discussion) are appropriate -- each occurrence serves a different rhetorical purpose.

## 5. Self-Citation Phrasing

No first-person references to prior work detected. All uses of "we" and "our" refer to the current paper's contributions (e.g., "our framework selects..."), which is standard. No patterns like "our prior work showed" or "we previously demonstrated \cite{...}" found.

## 6. Citation Formatting

- All `\cite{}` commands are well-formed (no missing braces, no stray spaces before `{`).
- Multi-cite format is correct: `\cite{key1, key2}` with comma separation.
- No `\cite` commands span line breaks.

---

## Summary

| Check                        | Status | Issues |
|------------------------------|--------|--------|
| Orphan cites (no bibitem)    | PASS   | 0      |
| Uncited bibitems (dead refs) | PASS   | 0      |
| Alphabetical ordering        | PASS   | --     |
| Double citations             | PASS   | 0      |
| Self-citation phrasing       | PASS   | 0      |
| Formatting                   | PASS   | 0      |

**25 cite keys, 25 bibitem keys, 1:1 alignment. No action required.**
