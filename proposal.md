# 📐 Proposal: Sequence Table Refactor — An Indirect Solution

> **Status:** Draft — for discussion
> **Audience:** Product Owner / Business stakeholder
> **Created:** 2026-06-08
> **Type:** Engineering investment — refactor, not feature
> **Target window:** ~6 weeks manual **/ ~4 weeks AI-assisted** (see § 10a)

---

## 1. TL;DR

We will not promise that the issues users see on the sequence table today — **white space during fast scroll, layout inconsistencies, occasional loading-state glitches** — will be **100 % solved** by this work. The team has already tried to patch these symptoms about 23 times.

What we **are** proposing is a different kind of solution: an **indirect solution**. Instead of patching the symptoms again, we restructure the underlying code so that **every future fix lands faster, cheaper, and safer**. The white-space bug itself becomes a future ticket — but a far easier one to close.

**Cost:** ~6 weeks manual / ~4 weeks with AI. **Rollback:** flip a switch — zero redeploy.

---

## ❓ Reviewer's four questions — direct answers

These four questions were raised in review. Short answers here; detail in the linked sections.

**Q1 — Will this refactor solve the issue, and how long to finish?**
Not directly, and that is deliberate. The refactor (~6 weeks manual / ~4 weeks with AI) does **not** itself fix the white-space bug. It turns the fix into a small, isolated follow-up scheduled for **Week 7**, estimated at **~1.5 days with ~70 % first-attempt success** (vs. ~10 % today). Realistic answer: *the issue is targeted the week after cutover and most likely closed within a few days of focused work — not inside this 6-week window.* If you need a guaranteed fix sooner, see § 13. → detail in § 7, § 9, § 13.

**Q2 — What are the possible causes that impact this issue?**
White-space-on-fast-scroll in a virtualized table almost always comes from a small set of suspects. Today they are tangled together so no one can isolate which one fires. After refactor each suspect lives in its own file and can be tested alone. Full candidate list → new **§ 7a**.

**Q3 — Can the refactor and normal development run in parallel?**
Partially. **Yes** for anything outside the sequence-table view (~70 % frontend throughput continues, ~100 % backend). **No** for new work *inside* the view — that is queued to Week 7 to avoid expensive rework. Full breakdown → § 8.

**Q4 — I use AI. How much time does that cut?**
The build phases (reading the 4,778-line file, scaffolding the 5–6 modules, generating tests, building the parity-diff harness) compress a lot with AI. The validation phases (QA parity sweep, 2-day production soak, stakeholder demos) are **calendar-bound and do not compress**. Net: **~6 weeks → ~4 weeks**. AI-assisted timeline → new **§ 10a**.

---

## 2. The problem today

The sequence table — plus eleven unrelated parts of the app — lives in **one file with 4,778 lines**. Everything is mixed in.

- Bug fixes in this area take ~5 days on average and break something else roughly 1 in 4 times.
- The same problem has been "fixed" 23 times without success.
- New developers need 4–6 weeks before they can contribute meaningfully here.
- Any change requires a full manual QA sweep because nothing is isolated.

This isn't just a developer inconvenience — it slows down every roadmap item that goes near this part of the app.

---

## 3. What we propose — refactor

**Refactor** means rewriting code **without changing what the user sees**, in a way that makes the code easier for the next developer to understand and modify.

We split the same 4,778 lines into **5–6 small files**, each focused on one clear job:

- **Layout** — column widths, padding, alignment (the place where "white space" complaints get fixed by design)
- **Scrolling** — how the long list moves and remembers its place
- **Loading** — when to show or hide the loading overlay
- **Cards** — how each card displays inside a cell
- **Grouping & rarities** — expand / collapse, rarity badges
- **Sticky elements** — row numbers, back-to-top buttons

Everything is built **beside** the old version, behind a switch. When the new version is ready, we flip the switch. If anything breaks, we flip it back — instantly, no emergency deploy.

---

## 4. Why refactor — three concrete payoffs

**1. The code becomes organized, not gigantic.**
A new developer can understand one small file (~300 lines) in about 30 minutes. Today, the same understanding takes ~12 hours of reading the giant file.

**2. Finding the cause of bugs becomes faster.**
A "white space" report goes straight to the Layout file. Today, the developer has to guess among five possible causes scattered across the giant file. We expect the first-attempt fix rate to rise from roughly **10 %** (which is why we are at Round 23) to roughly **70 %**.

**3. Adding new features and debugging becomes easier.**
- New feature: change one file, leave the others untouched. No more "I changed one thing and three others broke."
- Debugging: each file has its own small tests. When a test fails, we know what broke before the user does.

---

## 5. What this means for the business

The refactor is invisible the day after release. The benefit shows up three months later, when the team has done normal bug-fix and feature work in this area several times:

| Today | After refactor |
|---|---|
| Bug fix takes ~5 days; breaks something else ~1 in 4 times | ~1.5 days; breaks something else ~1 in 20 times |
| New feature: ~2 weeks of work + ~1 week of follow-up bug fixes | ~1 week of work + ~2 days of follow-up |
| New developer needs 4–6 weeks before contributing meaningfully | 1–2 weeks |
| QA on any change: full manual sweep, ~1 day | Targeted check on the affected file, ~2 hours |

These savings **compound**. Within ~3 months of normal work, the team has saved more time than this refactor cost.

---

## 6. Features touched by the refactor

The refactor touches every part of the sequence-table view. Owners should know what features the team needs to test — and what's temporarily at risk during the cutover.

### What IS touched (all need parity testing in Week 5)

| Feature | What it does |
|---|---|
| Vertical scrolling | The long list of rows the user flicks through |
| Horizontal scrolling | When there are many columns side-by-side |
| Text view (カード名) | Shows card numbers and names |
| Image view (画像表示) | Shows card images |
| View toggle (text ↔ image) | Preserves scroll position across the switch |
| Cylinder switch (左/右) | Preserves scroll position per cylinder |
| Loading overlay | The "loading" state shown while data is fetched and images decode |
| Hidden columns | Columns marked "hidden" that show masked data |
| Grouping | Expand / collapse for grouped cards |
| Rarity badges & popovers | Tap to see rarity info per cell |
| Sticky row numbers | The left column (1, 2, 3, …) that stays in place during horizontal scroll |
| **1枚目へ / 100枚目へ** buttons | Jump to top or bottom of the table |
| Pattern-match auto-scroll | Jumps to the row containing the search match |
| "Detail" button (modal) | Opens the card-detail modal from a cell |

### What is NOT touched

- The 1-table and 2-table views (used when fewer cards match)
- Search input, login, settings, gacha, history, and every other screen
- Backend / API / database
- The Go BE cutover work currently in progress

### Risk per feature

Any single feature above could regress during Weeks 5–6. The side-by-side comparison (Week 5) and the feature switch (Week 6) are the two safety nets. The switch lets us roll back instantly if anything slips through to production.

---

## 7. Can we find the root cause WITHOUT refactoring?

The owner is right to ask. Honest answer:

**We have already tried, 23 times.** Each attempt cost roughly 5 days. Each one failed for the same reason: the code is too tangled to isolate cause-from-effect — fixing one path triggers a different symptom elsewhere.

### The math

| Approach | Cost per attempt | First-attempt success | Risk of new regressions |
|---|---|---|---|
| Continue patching in place | ~5 days | ~10 % (we are at Round 23) | **High** — every patch can break something else |
| Refactor first, then fix | 6 weeks once + ~1.5 days per future fix | ~70 % | **Low** — each module is isolated and tested |

The team has already burned roughly **115 days** (23 attempts × 5 days) trying to find the root cause without refactoring. The 6-week refactor (~30 working days) would have paid for itself nearly **four times over** in that same period.

### The honest framing

- If we believe the **next** attempt has a 90 % chance of succeeding, refactoring is over-engineering. We should patch.
- But 22 prior failures suggest the opposite — the **next** patch is no more likely than the last one. **The structure of the code is the obstacle, not the developer's skill.**

### The indirect benefit

Even if the refactor itself does not directly find the root cause, the resulting modular structure makes the root cause **possible to find**. Today, no one on the team can confidently point to which of the 5 tangled subsystems is responsible. After refactor, the search space shrinks from 4,778 lines to ~300.

---

## 7a. Suspected causes of the white-space issue

These are **hypotheses to confirm in the Week-1 spike**, not confirmed findings. The point of listing them is twofold: (1) show the issue has a *bounded* set of likely causes, and (2) show why the refactor matters — today these suspects share one file and fire into each other, so we cannot test any of them in isolation. After refactor, each suspect lives in the module named in the right-hand column and can be reproduced and fixed on its own.

| # | Candidate cause | Why it shows up as white space | Owning module after refactor |
|---|---|---|---|
| 1 | **Virtualization window lag** | On fast scroll the visible window advances faster than rows mount → a blank gap until rendering catches up | Scrolling |
| 2 | **Wrong / estimated row heights** | If row height is estimated and the real height differs, the spacer math is off and gaps open between rows | Layout |
| 3 | **Async image decode** | Cells reserve space but images decode asynchronously; if the area paints before decode, cells flash empty | Cards + Loading |
| 4 | **Overlay hides too early** | The loading overlay lifts on *data arrival* instead of *render complete*, exposing not-yet-painted rows | Loading |
| 5 | **Scroll-restore jump** | Restoring scroll position after a view toggle or cylinder switch lands before layout settles → momentary blank | Scrolling |
| 6 | **Sticky / offset miscalculation** | Sticky row numbers or header offsets throw the content-region geometry off by a few px under fast scroll | Sticky elements |

**Why this is the strongest argument for refactoring first:** the most likely culprit (#1, #2, #5) all live in *scrolling + layout* — and today that logic is interleaved with loading, cards, grouping and sticky behaviour in the same 4,778-line file. A fix for #1 routinely triggers a regression that looks like #4. Isolating them is precisely what makes Round 24 different from Rounds 1–23.

---

## 8. Can refactor and new development happen in parallel?

**Partially.** Here is the breakdown.

### ✅ Can run in parallel — no conflict

| Type of work | Why it's safe |
|---|---|
| Backend / Go BE cutover | Different codebase entirely |
| Search input, login, gacha, history, settings | Different files; refactor does not touch them |
| The non-virtualized 1-table / 2-table view | Explicitly out of refactor scope |
| Bug fixes outside the sequence-table view | No code overlap |

Expect roughly **70 % of normal feature throughput** in unrelated areas to continue uninterrupted.

### ⚠️ Should be paused — will conflict

| Type of work | Why it conflicts |
|---|---|
| Any new feature touching the sequence-table view | Refactor changes the file structure; features written against the old structure need expensive rework after Week 6 |
| Any "Round 24" patch attempt at the white-space bug | Will be discarded by the refactor; effort wasted |
| Loading-overlay or scroll-restoration changes in this view | Directly inside refactor scope |

These should be **queued for after Week 6**. The wait is short, and the result lands on the clean structure instead of compounding the problem.

### 🟡 Case-by-case — coordinate weekly

| Type of work | Decision rule |
|---|---|
| Changes to the MobX store the table reads | Additive changes (new fields, new methods) are safe in parallel. Changes to existing fields the table reads → coordinate. |
| Shared utility helpers (e.g. `isCardCellHidden`) | Refactor extracts these to a shared file. Concurrent edits → merge weekly. |
| Visual / Tailwind theming | Safe if global; coordinate if they touch sequence-table-specific classes. |

### Capacity headline

The team is **not** at 0 % throughput during the refactor. It is at:
- ~0 % on sequence-table work (intentional, being refactored)
- ~70 % on other frontend work (FE1 dedicated to refactor, FE2 split; ~1 FTE remaining for unrelated work)
- ~100 % on backend / Go BE (separate team, no coupling)

The PM should communicate this split to stakeholders so feature commitments outside the refactor area stay honest.

---

## 9. What this work explicitly does NOT include

- ❌ It does **not** directly fix the "white space during very fast scroll" race condition. That becomes a follow-up ticket, which will be far cheaper to close after the refactor.
- ❌ It does **not** ship new user-facing features.
- ❌ It does **not** touch the 1-table or 2-table view (which works fine today).
- ❌ It does **not** require a backend change or a database migration.

---

## 10. Timeline — 6 weeks, refactor track + parallel-dev track

**Assumed team:** 1 PM, 2 Frontend engineers (FE1 senior, FE2), 1 QA, 1 UX. None loaded above ~70 % on this work.

### Two tracks running side-by-side

| Week | Refactor track (FE1 + FE2 + QA + UX) | Parallel-dev track (remaining capacity) |
|---|---|---|
| **1** | Foundation: scope confirmation, baseline screenshots, set up new code structure beside old, 1-day spike to surface hidden surprises | Continue in-flight work outside sequence table. Pause new sequence-table tickets. |
| **2** | Build the **Layout** file — white-space + padding fixed by design. New version visible only to developers. | Go BE cutover continues. Unrelated bug fixes continue. |
| **3** | Build scrolling, view modes (text/image), hidden columns, grouping. | Same as Week 2. New features that touch the sequence table stay queued. |
| **4** | Add rarity popovers, back-to-top buttons, scroll memory, pattern-match auto-scroll. Internal demo: "old vs. new, side by side". | Same. PM begins planning the post-refactor backlog so Week 7 starts productive. |
| **5** | Side-by-side comparison: same data, both versions, confirm identical behavior. QA sweeps every scenario from § 6. | **Reduced** — QA capacity drops because they're running the parity sweep. Unrelated QA work delayed 2–3 days. |
| **6** | Cutover. 2-day production soak. Delete old code. Retrospective. | Parallel work resumes full capacity by mid-week. |
| **7+** | — | Sequence-table backlog (queued from Weeks 1–6) reopens. **First ticket on the refactored code is the white-space race-condition itself.** |

### Throughput estimate over the 6 weeks

| Area | Capacity vs. normal |
|---|---|
| Sequence-table feature work | 0 % (intentional — being refactored) |
| Other frontend features | ~70 % |
| Backend / Go BE | ~100 % |
| QA on unrelated work | ~60 % (drops in Weeks 5–6 for parity sweep) |
| UX | ~80 % (light: Week 1 layout spec, Week 6 sign-off) |

### Milestones the owner sees

| End of week | Milestone | Demo |
|---|---|---|
| W1 | Foundation set, surprises catalogued | Spike report + baseline screenshots |
| W2 | New table renders cleanly (behind a switch) | Side-by-side demo: old vs. clean layout |
| W4 | Feature-complete behind the switch | Walkthrough of every scenario on the new version |
| W5 | Parity proven | QA sign-off report |
| W6 | In production | Dashboard + retrospective |

**Bottom line for the owner:** the refactor does not freeze the team. It freezes **one area of the product for 6 weeks** while everything else continues at a reduced but reasonable pace.

---

## 10a. AI-assisted timeline — ~4 weeks instead of ~6

With AI assistance the **build and read** phases compress sharply; the **validation** phases do not, because parity testing, the production soak, and stakeholder demos are bound by calendar time and human judgement, not typing speed.

| Week | Refactor track **with AI** | Where AI helps |
|---|---|---|
| **1** | Foundation + spike. AI maps the 4,778-line file, drafts module boundaries, and ranks the § 7a candidate causes faster than manual reading. | Audit/read: ~3 days → ~1 day |
| **2** | Build Layout + Scrolling + Loading. AI scaffolds each module and generates unit tests; engineers review and correct. | Build weeks 2–3 fold into ~1.5 |
| **3** | Build Cards + Grouping + Sticky; wire the feature switch. AI generates the old-vs-new parity-diff harness. | Test + harness authoring |
| **4** | Parity sweep → cutover → 2-day soak. **Human-bound: AI does not shorten QA sign-off or the soak.** | — (intentionally unchanged) |

### What AI does and does not change

| Phase | Manual | With AI | Why |
|---|---|---|---|
| Read / audit the giant file | ~3 days | ~1 day | AI summarises and cross-references quickly |
| Scaffold 5–6 modules + tests | ~2.5 weeks | ~1.5 weeks | AI drafts; engineer reviews and corrects |
| Parity sweep (QA) | ~1 week | ~1 week | Calendar-bound — no change |
| Production soak | 2 days | 2 days | Calendar-bound — no change |
| **Total** | **~6 weeks** | **~4 weeks** | Savings are entirely in build/read |

> ⚠️ **Honest caveat:** AI speeds up *writing* the refactor — it does **not** lower the bar for reviewing it. AI-generated module splits need *more* careful human review, not less, because a subtle behaviour change is exactly what a parity sweep must catch. The 4-week number assumes the same QA rigour as the 6-week plan, not a thinner one.

---

## 11. Manual flow — what we do when something goes wrong

We deliberately keep every safety lever **manual**, not automated. This keeps cost down and the team in direct control.

- **If a user reports a regression in production** → on-call engineer flips the feature switch **off**. Users instantly see the **old** version again. No emergency deploy, no engineer paged at 2 AM. The bug is investigated calmly the next working day.
- **If QA finds a parity bug during Week 5** → cutover delayed by 2–3 days. FE1/FE2 fix, QA re-tests. We do not ship until the comparison is clean.
- **If a stakeholder requests a new feature mid-refactor** → request acknowledged, added to the backlog after Week 6. The refactor scope is **not** bent. This is the most common cause of refactor-project failure — protecting against it is the PM's primary job for these 6 weeks.
- **If the timeline slips by more than 3 days in any single week** → PM raises it at the next standup. Team picks one of three options: cut a non-critical scope item, add a 7th week, or accept a smaller parity window in Week 5. Decision communicated to stakeholders within 24 hours. No silent slippage.
- **If the team finds the old code is worse than estimated** → we do **not** abandon partway. We re-estimate and propose a revised plan (likely Week-7 buffer). The work already done is not wasted; the new structure pays off even on a delayed timeline.

---

## 12. Risks

| Risk | What could go wrong | How we handle it |
|---|---|---|
| Hidden complexity | The 4,778-line file has logic we didn't see in the audit | Week-1 spike specifically to surface surprises before estimating hardens |
| Time slippage | Refactor takes longer than 6 weeks | Week 7 buffer pre-approved; slippage communicated immediately |
| Scope creep | Stakeholder asks for new features mid-refactor | Strict "parity only" mandate; new ideas go to the backlog |
| Engineer fatigue | Reading the original code is exhausting | Pair programming; rotate hard pieces between FE1 and FE2 |
| Parallel-track conflict | Someone ships a sequence-table change anyway | PM gate-keeps the queue in § 8; conflicting tickets are rejected, not silently merged |
| User confusion | Layout looks subtly different after refactor | UX prepares a one-paragraph release note; the visible changes are small and net-positive |
| Old code never deleted | Two versions live in parallel forever → double maintenance | Deletion in Week 6 is a gate, not optional |

---

## 13. Say it twice on purpose

Repeating § 1 because this is the most important thing to internalize before approving:

- We are **not** fixing the white-space-during-fast-scroll race condition this sprint.
- We **are** making it dramatically cheaper to fix that bug — and every future bug like it — afterwards.

If the owner needs a **direct, definitive fix to the white-space bug right now**, this proposal is the wrong direction and we should discuss a different plan first.

If the owner accepts that **Round 24, 25, and 26 are coming under the current architecture**, and wants to **end that pattern**, this proposal is the right direction.
