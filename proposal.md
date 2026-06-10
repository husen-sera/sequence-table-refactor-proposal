# 📐 Proposal: Stabilize & Speed Up the Sequence Table — A One-Time Investment

> **Status:** Draft — for approval
> **Audience:** Business Owner / Product Executive / Engineering Manager
> **Created:** 2026-06-08
> **Type:** Risk-reduction & delivery-reliability investment
> **Investment window:** ~6 weeks (manual) / ~4 weeks (AI-assisted)
> **Customer-facing risk:** Low — protected by instant one-click rollback

> *All time and percentage figures in this document are **estimates and projections** drawn from our issue-tracking history. They are planning numbers, not guarantees.*

---

## 1. Executive Summary *(30-second read)*

**The problem.** One specific area of our product — the sequence table — is expensive to maintain and unpredictable to change. A recurring customer-visible glitch (blank/white space during fast scrolling) has resisted **an estimated 23 fix attempts**. Each attempt costs roughly a week of engineering time and has roughly a **1-in-4 chance of breaking something else.**

**Why now.** Every month we delay, we spend more money re-investigating the same problem, we slow down unrelated features that pass through this area, and we deepen our reliance on the few engineers who understand it. The cost is compounding.

**What happens if we do nothing.** The fix-and-break cycle continues ("Round 24, 25, 26…"), maintenance costs keep rising, feature delivery in and around this area stays slow, and the risk of a customer-visible regression remains elevated.

**Expected business benefit.** A one-time investment to restructure this area is **projected** to raise our first-time fix success from roughly **10% to roughly 70%**, cut the cost of future changes substantially, shorten new-developer onboarding here from **4–6 weeks to 1–2 weeks**, and reduce the risk of recurring production issues. Based on our history, the investment is **anticipated to pay for itself within about three months** of normal work.

**Rollout risk & mitigation.** Low. The new version is built **alongside** the existing one and switched on only when proven equivalent. If anything looks wrong, we revert instantly with a single switch — **no emergency deployment, no customer downtime.** No backend, billing, or database changes are involved.

**The decision in one line:** *Continue paying a recurring, compounding cost with low odds of resolution — or make a bounded, low-risk, one-time investment that is expected to reduce risk and increase delivery speed for years.*

---

## 2. Business Justification

Today, this part of the product carries a set of business costs that are easy to overlook because they are spread across many small events rather than one large bill:

| Business cost | What it looks like today | Why it matters to the business |
|---|---|---|
| **High maintenance cost** | A single bug here takes an estimated ~5 days to fix | Engineering hours spent maintaining, not building |
| **Slower feature delivery** | Anything touching this area moves slowly and cautiously | The roadmap stalls whenever it passes through here |
| **Recurring production issues** | The same defect has returned across ~23 attempts | Repeated customer-visible glitches erode trust |
| **Higher regression risk** | ~1 in 4 fixes breaks something else | Each change can create the *next* incident |
| **Long onboarding** | New developers need 4–6 weeks before they're productive here | Slow ramp-up, lost hiring leverage |
| **Key-person dependency** | Only a few engineers can safely touch this area | A business-continuity risk if they're unavailable |

This proposal is **not** about code aesthetics. It is about **reducing operational risk, improving delivery predictability, and lowering the ongoing cost** of one of the more fragile areas of the product.

---

## 3. Cost of Inaction

> **The core question: what is the business cost of *not* making this investment?**

If we continue with the current approach, we should expect:

- **Continued bug-fix cycles without permanent resolution.** The defect has survived ~23 attempts; there is no evidence the 24th behaves differently. We are, in effect, **renting a fix instead of buying one.**
- **More engineering time spent re-investigating the same area.** Each cycle is an estimated ~5 days. By our tracking, roughly **115 working days have already been consumed** this way — and the meter keeps running.
- **Delayed feature development.** Every cycle spent here is capacity *not* spent shipping customer-facing value (see § 4).
- **Growing maintenance cost over time.** As this area accumulates more patches, it becomes harder — not easier — to change, so each future fix tends to cost *more* than the last.
- **Increased risk of future regressions.** With a ~1-in-4 break rate, continued patching keeps the probability of a new customer-visible incident elevated.
- **Slower response to customer issues.** When a customer reports a problem here, our current best case is a multi-day investigation with low odds of a clean fix.

**Plain summary:** doing nothing is *not* the zero-cost option. It is the **higher-cost option, paid in instalments**, with the bill growing over time.

---

## 4. Opportunity Cost

Every week spent re-investigating the same recurring issue is a week **not** spent delivering new customer value.

- **Features delayed.** Engineering capacity tied up in repeat investigations is capacity unavailable for roadmap work.
- **Roadmap impact.** Because this area is slow and risky, initiatives that pass through it are quoted higher and scheduled later — or quietly avoided.
- **Capacity consumed by recurring investigations.** The estimated ~115 days already spent here represent **roughly half a year of one engineer's time** that produced no durable resolution.
- **Reduced ability to innovate.** Teams that spend their time firefighting the same area have less room to experiment, improve the product, and respond to market opportunities.

The opportunity cost is the **invisible line item**: we don't see the features we *didn't* ship because the capacity was absorbed here.

---

## 5. Team Scalability *(why this matters for business continuity)*

A core benefit of this investment is that it makes the team **more scalable and less fragile** — which directly protects business continuity.

| Today | After the investment (projected) |
|---|---|
| New developers need **4–6 weeks** to safely contribute here | **1–2 weeks** — work is organized into small, understandable pieces |
| Knowledge is concentrated in a few engineers | Knowledge is **distributed**, because each piece is small and self-contained |
| The business depends on specific individuals to touch this area | **Reduced key-person dependency** — more engineers can work here safely |
| New features in this area are slow and risky | **Faster future feature development** on a stable foundation |
| Maintenance is expensive and nerve-wracking | **Easier, lower-risk maintenance** |

**Why the business should care:** reducing dependence on individual engineers lowers the risk that a departure, an illness, or a competing priority stalls an important part of the product. It also improves **hiring leverage** — new engineers become productive sooner, so each hire delivers value faster.

---

## 6. Return on Investment

> *Figures below are **estimates and projections** for planning, not guarantees.*

**The narrative, in plain terms:**

| | **Current State** | **Future State (projected)** |
|---|---|---|
| Diagnosing an issue | Repeated, multi-day investigations | **Faster issue isolation** |
| Building a change | Slow, cautious, high follow-up cost | **Faster development** |
| Ongoing upkeep | High maintenance effort | **Lower maintenance overhead** |
| Stability | Elevated regression risk | **Reduced operational risk** |
| First-time fix success | ~10% | **~70% (anticipated)** |

**The simple ROI math (estimated):**

- We have **already spent an estimated ~115 days** patching this area without durable success.
- This investment is **~30 working days** (≈6 weeks; faster with AI assistance).
- On those figures, the investment would have **paid for itself nearly four times over** within the time we've already spent — and it is **anticipated to recover its cost within roughly three months** of normal work going forward.

**An honest framing for the decision-maker:**
- *If* we genuinely believed the **next** patch attempt had a high chance of permanently resolving the issue, this investment would be unnecessary — we should just patch.
- *But* the pattern of ~23 prior attempts strongly suggests the next patch is **no more likely to succeed than the last.** That is precisely why a structural investment — not another patch — is the lower-risk, higher-return path.

---

## 7. Risk & Mitigation

This is fundamentally a **risk-reduction** initiative — but we also manage the (low) risk of the work itself carefully. The headline: **customers are protected throughout.**

| Risk | Likelihood / impact | How we mitigate it |
|---|---|---|
| **A customer notices a difference after rollout** | Low | The new version ships **behind a switch**, validated **side-by-side** against the current one before anyone sees it |
| **A problem reaches production** | Low | **Instant one-click rollback** to the proven version — no emergency deployment, no downtime |
| **The work takes longer than planned** | Medium | A 7th-week buffer is **pre-approved**; any slippage is reported within 24 hours — **no silent delays** |
| **Scope expands mid-flight** | Medium | Strict "match today's behavior only" mandate; new ideas are scheduled for after rollout |
| **Hidden complexity surfaces** | Medium | A dedicated first-week investigation surfaces surprises **before** estimates are finalized |
| **Increased dependency on one engineer** | Reduced | The work is shared across the team and **lowers** key-person dependency by design |

**Why delivery confidence is high:** there are **no backend, billing, or database changes**, the change is reversible at any moment, and the team sees a working demonstration at the end of each key week (see § 8).

---

## 8. What You Will See — Milestones & Confidence Checkpoints

You do not have to take the plan on faith. Each key week ends in a visible, reviewable result:

| By end of | Milestone | What you'll see |
|---|---|---|
| **Week 1** | Investigation complete | A findings report; no surprises hidden |
| **Week 2** | Core area rebuilt (hidden behind a switch) | A side-by-side demo: today's version vs. the improved one |
| **Week 4** | Feature-complete (still hidden) | A walkthrough confirming every behavior is preserved |
| **Week 5** | Equivalence proven | A sign-off that the two versions behave identically |
| **Week 6** | Live in production | A stability dashboard + a short retrospective |

**Bottom line:** this investment does **not** freeze the team. It focuses on **one area** for a few weeks while the rest of the roadmap continues at a reasonable pace, and it is **reversible at every step.**

---

## 9. The Recommendation

We recommend approving this **one-time, bounded, low-risk investment** now.

- It is **expected** to convert a recurring, compounding cost into a **stable, predictable** part of the product.
- It is **projected** to increase delivery speed, reduce operational risk, and shorten onboarding.
- It is **anticipated** to pay for itself within roughly three months — and to keep returning value for as long as we operate this product.

If your immediate priority is instead a **guaranteed same-day patch** to the visible glitch, that is a legitimate choice and we can plan for it separately — but it would not address the underlying, compounding cost. **This proposal is the path that ends the cycle.**

---

---

# Appendix — Technical Reference *(for the engineering team)*

> The sections below support the business case above with implementation detail. **Business stakeholders do not need to read further** — the decision rests entirely on §§ 1–9.

## A1. What "restructuring" means here

The functionality lives in a **single 4,778-line file** that also contains eleven unrelated parts of the app. "Restructuring" (refactoring) means reorganizing that code **without changing what the user sees**, splitting it into **5–6 small, single-purpose files**:

- **Layout** — column widths, padding, alignment (where the "white space" is fixed by design)
- **Scrolling** — how the long list moves and remembers its place
- **Loading** — when to show/hide the loading overlay
- **Cards** — how each card displays inside a cell
- **Grouping & rarities** — expand/collapse, rarity badges
- **Sticky elements** — row numbers, back-to-top buttons

The new version is built beside the old one behind a feature switch; cutover and rollback are both a single toggle.

## A2. Suspected causes of the white-space issue *(hypotheses, to confirm in Week 1)*

These are **candidate causes, not confirmed findings.** They share one file today and interfere with each other, which is why they cannot currently be isolated.

| # | Candidate cause | Why it appears as white space | Owning module after split |
|---|---|---|---|
| 1 | Virtualization window lag | Visible window advances faster than rows mount → blank gap | Scrolling |
| 2 | Wrong / estimated row heights | Spacer math drifts → gaps between rows | Layout |
| 3 | Async image decode | Area paints before images decode → cells flash empty | Cards + Loading |
| 4 | Overlay hides too early | Overlay lifts on data arrival, not render-complete | Loading |
| 5 | Scroll-restore jump | Position restored before layout settles → momentary blank | Scrolling |
| 6 | Sticky / offset miscalculation | Geometry off by a few px under fast scroll | Sticky elements |

The likeliest culprits (#1, #2, #5) all sit in *scrolling + layout*. Isolating them is what makes this attempt structurally different from the previous 23.

## A3. Features in scope (require equivalence testing before cutover)

Vertical & horizontal scrolling · text view (カード名) · image view (画像表示) · view toggle · cylinder switch (左/右) · loading overlay · hidden columns · grouping · rarity badges & popovers · sticky row numbers · 1枚目へ / 100枚目へ jump buttons · pattern-match auto-scroll · "Detail" modal.

**Out of scope (zero risk):** the 1-table & 2-table views · search, login, settings, gacha, history · backend / API / database · the in-progress Go BE cutover.

## A4. Parallel work during the investment

| Can run in parallel (~70% FE throughput continues) | Should pause (queued to after rollout) | Coordinate weekly |
|---|---|---|
| Backend / Go BE cutover (separate codebase) | New features touching the sequence-table view | Changes to the shared data store the table reads |
| Search, login, gacha, history, settings | Any further "Round 24" patch attempt | Shared utility helpers |
| The 1-table / 2-table view | Loading/scroll changes inside this view | Global theming |
| Bug fixes outside this view | | |

Capacity during the window (estimated): ~0% on sequence-table feature work (intentional), **~70%** on other frontend work, **~100%** on backend.

## A5. Timeline detail

**Manual (~6 weeks):** W1 foundation + investigation · W2 Layout · W3 scrolling/view modes/grouping · W4 remaining features + internal demo · W5 equivalence sweep · W6 cutover + 2-day soak + remove old code.

**AI-assisted (~4 weeks):** AI compresses the **read/build** phases (file audit ~3 days → ~1 day; module scaffolding ~2.5 weeks → ~1.5 weeks). The **validation** phases — equivalence testing and the production soak — are calendar-bound and **do not** compress.

> ⚠️ AI speeds up *writing* the change, not *reviewing* it. The ~4-week estimate assumes the **same testing rigor** as the 6-week plan, not a reduced one.

## A6. Engineering-side risks

| Risk | Mitigation |
|---|---|
| Hidden complexity in the original file | Week-1 investigation before estimates harden |
| Time slippage | Pre-approved Week-7 buffer; 24-hour slippage reporting |
| Scope creep | Strict "match today's behavior only" mandate |
| Engineer fatigue on dense code | Pair programming; rotate the hard pieces |
| Someone ships a conflicting change | The work queue is gate-kept, not silently merged |
| Old code never removed | Removal is a required gate at cutover, not optional |
