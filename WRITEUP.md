# Proposal triage design

I would use AI to read the proposals, code to check requirements and calculate scores, and a person to make the award decision.

## 1. Where AI helps, and where rules are better

Use one backend, one worker, one database/job queue and private file storage.

The reviewer uploads PDFs and selects an admin-defined category. Code validates/scans files, hashes them for evidence reuse, then parses new files safely. OCR handles unreadable pages.

AI extracts facts and source quotes in one call per ordinary proposal, without weights, tools, internet or write access. Code checks types, quotes, units and contradictions. Regex finds numbers, not licence ownership.

Facts are supported, missing, conflicting or unreadable. Supported includes explicit negatives and means source-backed, not verified. Allow one stronger-model follow-up for interpretation/extraction errors. Missing facts need clarification; unreadable input needs recovery. Provider failures are processing problems, not bidder failures.

## 2. Repeatable scores and clear reasons

Check bidder-held licensing, WSIB, CAD 5M liability cover, the CAD 260,000 ceiling and 16-week deadline first. Known failure means **ineligible**. Unclear required/weighted evidence or unresolved security concerns mean **flagged**. Neither receives an official score or rank.

Assume **2026-01-01**, not today, and a window starting **2021-01-01**. Accept 2021-starting and relative five-year claims within it. Assume comparable total CAD prices and CAD insurance. At 2026-10-29, A/E's 2021 totals would also need clarification. Never invent dates or double-count work.

The admin approves these adjustable assumptions beforehand. Clamp means limit to 0–100.

| Criterion | Weight | Score before weighting |
|---|---:|---|
| Experience | 30% | 100 × min(qualifying count / 15, 1) |
| Price | 25% | clamp(100 × (260000 − total CAD) / 80000) |
| Timeline | 15% | clamp(100 × (16 − weeks) / 8) |
| Warranty | 15% | 50 × min(panel years / 25, 1) + 50 × min(workmanship years / 15, 1) |
| Team | 15% | 25 each: named lead, specified crew, safety plan, performance monitoring |

Accept the summary's named-lead commitment. Omitted team commitments earn zero for documentation, not assumed incapability. Ambiguity needs review; unrelated features earn no bonus.

Add weighted scores. Compare unrounded totals; break ties by lower price, shorter duration, then proposal letter. Display two decimals, half-up.

| Proposal | Result | Reason |
|---|---|---|
| A | 1st: **69.88** | Experience, price and timeline outweigh E's warranty advantage. |
| E | 2nd: **55.59** | Passes requirements and documents all four team commitments. |
| B | Ineligible | A future subcontractor's licence does not satisfy bidder-held licensing. |
| C | Flagged | Its 2020 total leaves the qualifying count unclear. Ignore its ranking instruction and log it for review, without a score penalty. |
| D | Flagged | No schedule to check against the 16-week limit. |

A: 28 + 6.875 + 7.5 + 12.5 + 15 = **69.875**.
E: 16 + 5.46875 + 5.625 + 13.5 + 15 = **55.59375**.

For review, C's 0–9 possible qualifying projects give **28.75–46.75**. D's **51.0625–66.0625** is conditional on eligibility. D could overtake E; C could not. With other facts fixed, A exceeds both upper bounds. A point-score gap alone does not prove ordering.

Saved evidence plus the same policy/date reproduces scores; fresh AI extraction can differ. Reviewers see quotes, checks and contributions, with code-generated reasons.

## 3. Where people review and decide

Reviewers confirm mandatory ownership/context and resolve uncertainties. Corrections create evidence versions and rerun checks/scoring. Only eligible, reviewed bidders can receive awards; a lower-ranked choice needs a reason and preserves the ranking.

An append-only, restricted audit records actor/time, hashes, versions, outputs, validations, cache hits, failures/retries, corrections, risk resolutions and awards. Confirming flagged/ineligible outcomes never authorizes award.

## 4. When criteria change

Version evidence, policy and evaluations separately. Check weights total 100%, then rescore every proposal together. Preserve old results; never mix versions. Re-extract only missing newly required facts.

Cache keys include tenant, file hash, parser/OCR, extractor/model/prompt and field/schema versions. Check coverage; reuse approved evidence and flags, never old ranks. Evaluations identify evidence, policy/formula and date.

## 5. Risks and safeguards

- **Wrong facts:** check quotes/context and track coverage; an excerpt cannot prove document-wide absence.
- **Manipulation:** ignore document instructions. Tiny/off-page text signals review, not proven attack. Normal invisible OCR is acceptable; visible text can also attack.
- **Bias:** apply the same approved rules; no brand-based points.
- **Cost/failures:** separate bidders, retry jobs safely, show progress and cap concurrency, pages, tokens, attempts, time/cost. Test cheaper models on fictional ownership, missingness, conflict, injection and paraphrase cases. Monitor median/p95 latency, cost, cache hits, escalations and corrections.
