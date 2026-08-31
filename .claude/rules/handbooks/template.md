---
title: "ConceptName"
type: concept
tags: [curriculum, <topic-slug>, <difficulty>]
sources: [source-slug-1, source-slug-2]
last_updated: YYYY-MM-DD
---
## Core Thesis
> One sentence capturing the soul of the concept. Tutor regresses here when the learner loses the thread.

## Problem Definition
What problem does this concept solve? What can the learner *not* do before vs. after?

## Terminology
| Term | Definition |
|------|------------|
| Term1 | Definition1 |

## Mental Model
ASCII diagram or structural description. The tutor guides the learner to *draw* it, not to show it directly.

## Minimal Implementation
The "130 LOC boundary" — smallest correct form. Explicitly note what is deliberately *not* covered here (deferred to later concepts).

## System Position
- Inherits from: [[Prereq1]], [[Prereq2]]
- Prepares for: [[NextConcept1]], [[NextConcept2]]
- Cross-links: [[RelatedConcept]]

## Common Errors (Behavioral)
| Error | Symptom | Detection Prompt |
|-------|---------|-----------------|
| Error1 | What goes wrong | Question to surface it |

## Diagnostic Questions (Step 1)
1. ...
2. ...

## Question Bank (Step 3b — Socratic)
### Entry-level
- ...
### Advanced
- ...

## Hint Escalation Ladder (Step 3c)
| Level | Hint |
|-------|------|
| L1 Rephrase | ... |
| L2 Guide | ... |
| L3 Analogy | ... |
| L4 Deconstruct | ... |
| L5 Worked Example | ... |

## Misconceptions (Step 3d — Cognitive)
| Misconception | Detection Question | Counter-example |
|---------------|-------------------|----------------|
| "..." | ... | ... |

## Mastery Check (Step 3g)
| Dimension | Check Question | Pass Criterion |
|-----------|---------------|----------------|
| Accurate | ... | ... |
| Causal | ... | ... |
| Application | ... | ... |
| Discrimination | ... | ... |

**Session mastery threshold**: all dimensions ≥75%, overall ≥80%.

## Practice Tasks (Step 3h)
### Task 1: [Name] [Required, Weight X%]
**Prompt**: ...

**Acceptance Criteria**
| ID | Item | Type | Pass Rule |
|----|------|------|-----------|
| AC-1 | ... | functional | ... |
| AC-2 | ... | edge-case | ... |
| AC-3 | ... | mechanism | ... |

**Scoring**
- PASS: all ACs pass
- NEEDS_WORK: core ACs pass, mechanism/edge missed (1 rework allowed)
- FAIL: any core AC fails

## Chapter-level Pass Criteria
Learner passes this concept iff:
1. Mastery Check (Step 3g) all dimensions ≥75%
2. Required Task(s) PASS
3. Total practice score ≥75%

**On FAIL**: return to Tutor Loop, do not advance.

## Memory Mnemonic
One sentence for session summary and spaced repetition.

## Navigation
- Previous: [[PrevConcept]]
- Next: [[NextConcept]]

---