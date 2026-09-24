# PLAN — kontrolor-test (single HTML quiz)

Date: 2026-09-24  
Repo: `https://github.com/mikki021/kontrolor-test` (private, currently empty)  
Source bank: `/workspace/materijal/extract/question-bank.json` (+ `.md`, summary, conflicts)  
Owner of plan: Pia  
Implement: @Reed · Review: @Nia · Validate: @Ivo · Bank: @Arhiva

## Problem

Election monitors (kontrolori) need a Drive-shareable self-test: one HTML file, ~40–45 multiple-choice questions pulled from a larger bank organized by training sections, with answer order reshuffled on every page load.

## Grill on Arhiva’s bank (before implement)

Bank is **usable after small cleanup** — 76 MCQs / 11 sections / 0 NEJASNO. Do not ship duplicates.

1. **Dedupe (required)**  
   - Drop **Q66** (keep **Q63** — same stem: which Zapisnik copy is posted for public view; correct = third copy per clanove slide 69).  
   - Drop **Q72** (keep **Q68** — same topic: ≤12h handover after close; clanove slide 70).  
   - After drop: **74** unique items.

2. **Thin section (accepted risk)**  
   - **Затварање бирачког места** has only **2** questions (Q52–Q53); clanove slide 52 empty in extract, so those cite TV.  
   - Accept for v1: always include both when drawing; do not invent new closing questions in the HTML. Optional follow-up for Arhiva if Miroslav wants a thicker section later.

3. **Inference flag**  
   - **Q21** tagged Inference (flag + voting start). Keep in bank; Nia/human may exclude later. Default: eligible for draw.

4. **Authority**  
   - For TV vs Članovi conflicts, bank already follows **Članovi only** (ЛИК on observer removal; 3rd Zapisnik copy; ≤12h handover). HTML must not “fix” from TV slides.

5. **Correct-option text**  
   - Prefer the cleaner Q63/Q68 wording (no “према obuka-clanove” in the option string shown to the user).

## User-visible behavior

1. Open `index.html` (double-click or Drive preview / download+open). No server, no build step.
2. On each load:
   - Draw **42** questions (range allowed **40–45**; lock **42** for v1) from the embedded bank **after** dedupe.
   - Cover **all 11 sections** when possible: take **min(available, max(1, round(42 × section_size / 74)))** then fill randomly to 42 without exceeding section size. Closing section will contribute its 2 whenever present.
   - Shuffle **question order**.
   - Shuffle **option order** per question; track correct by stable `id` / original key, never by display index.
3. User answers all; Submit shows score (correct/total) and which were wrong (optional: show correct answer after submit only).
4. “Потврди поново” / reload starts a fresh random draw + shuffle.
5. Language: Serbian (Cyrillic) as in the bank. No login, no analytics, no external CDN required (inline CSS/JS only) so Drive offline download still works.

## Out of scope (v1)

- Backend, auth, Google Forms, scoring server  
- Editing the bank in the UI  
- Inventing new questions to thicken Затварање  
- Commits/PRs from Pia (Reed owns the repo)  
- Political advice or live election operations

## Deliverables (Reed)

| Path | Purpose |
|---|---|
| `index.html` | Single self-contained quiz |
| `question-bank.json` | Cleaned bank (74 Qs; Q66/Q72 removed); embedded in HTML **or** loaded via relative fetch — **prefer embed** so one file on Drive works without sibling fetch quirks |
| `PLAN.md` | This plan (copy into repo) |
| `README.md` | How to open / share on Drive |

Prefer **one file on Drive**: embed the JSON (or a `const BANK = [...]`) inside `index.html`. Keeping a separate `question-bank.json` in the repo for review is fine; Drive share = the HTML alone.

## How to test (@Ivo)

1. Open `index.html` in Chrome/Safari twice → different question sets and/or option orders.  
2. Confirm ~42 questions; all 11 section names appear at least once when bank allows (closing: both Qs if drawn under coverage rules).  
3. Answer all correctly using known keys → 42/42.  
4. Answer wrong → score reflects misses; correct revealed only after submit.  
5. Offline / `file://` works with no console network errors.  
6. Spot-check Q63 and Q68 answers = third copy / 12 hours (not TV’s second copy / 18 hours).

## Risk

- Drive “preview” may sandbox scripts — document “Download → open locally” if preview blocks JS.  
- Section imbalance → closing always thin; coverage algorithm must not crash when a section has fewer items than its quota.  
- Encoding: keep UTF-8 Cyrillic.

## Slice for @Reed (one next slice)

Implement cleaned bank + single `index.html` per this PLAN; push to `mikki021/kontrolor-test`. Do not expand the bank. Then @Nia reviews PLAN + HTML; @Ivo validates the run checklist above.
