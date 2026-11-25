# GetHandyMan — Competitors Review

**Version:** 0.1 (draft) — November 2026

We reviewed six players: four international home-services marketplaces, the main Israeli competitor, and the new category of "AI price-from-photo" apps. For each, we note what it does, how it prices, and **what we learned from it**.

---

## 1. Homejoy (USA, closed 2015)

- **What it was:** An on-demand home-cleaning marketplace. It announced on July 17, 2015 that it would shut down on July 31, 2015.
- **Why it failed:** Several factors. The founder named worker-classification lawsuits as the deciding factor. Investigative reporting added operational problems: for months a flaw in the **scheduling algorithm** booked back-to-back jobs for cleaners **without accounting for travel time**. A former employee also pointed to an expensive international expansion and to **leakage**: the best workers left for direct arrangements with Homejoy's own customers.
- **What we learn:**
  - Matching must model **travel time / distance** (we use PostGIS and route-aware matching).
  - **Leakage is an existential risk.** Our fees are charged only on completed jobs and capped at ₪150, and the protection and change-of-scope arbitration exist only on-platform, so there is a reason to stay.

## 2. TaskRabbit (USA, owned by IKEA)

- **What it is:** A marketplace for errands and handyman tasks.
- **History of the pricing model:** TaskRabbit started with an **auction / bidding** model, in which contractors bid on tasks posted by clients. In June–July 2014 it **abandoned bidding**. It cited a falling task-completion rate and inefficient matching of supply and demand, and replaced the auction with **fixed hourly rates** and algorithmic matching. The change caused a strong backlash among Taskers in the US.
- **Fees today:** Clients pay a **service fee** on top of the Tasker's rate, which TaskRabbit **cut from 30% to 15% in 2018**, plus a separate **Trust & Support fee**. Both are percentages of the hourly rate.
- **What we learn:** Open bidding failed partly because bids are hard to compare and match efficiently. Our answer is to keep competitive quotes but give the customer a **common reference point**: the AI diagnosis, the scenario tree, market ranges, and ranking by expected final price.

## 3. Thumbtack (USA)

- **What it is:** A local-services marketplace that is **pay-per-lead**: professionals pay to access or be matched with a lead, **whether or not the job is booked**.
- **Pain points:** Large volumes of professional complaints about paying for leads that were fake, unresponsive or irrelevant, and about rising lead costs.
- **What we learn:** Charging professionals for leads misaligns incentives. **We charge only on completed jobs**, which is a direct selling point when recruiting professionals.

## 4. Urban Company (India)

- **What it is:** India's largest on-demand home-services platform (beauty, cleaning, repairs, AC service), now also in several other countries.
- **Pricing model:** **Hybrid.** Standardised services such as AC service and plumbing basics are sold at **fixed prices**, while non-standard work is handled separately without a fixed charge. The company also standardises professionals' behaviour (training, SOPs, background checks).
- **What we learn:** Fixed prices work for standard jobs, and quotes are needed for complex ones. Our scenario tree is effectively a **generalisation of this hybrid**: the high-probability scenario behaves like a fixed-price menu item, and the rare, expensive scenarios are priced in advance instead of being renegotiated on site.

## 5. Midrag (מידרג, Israel) — the local competitor

- **What it is:** An Israeli directory of recommended professionals with about 12,000 professionals across 149 fields and about 3 million customer reviews.
- **Ranking:** A list sorted by the **average score** from previous customers. A professional **cannot buy** their position, and to be listed they must reach an overall score of at least 9.
- **Pricing information:** Midrag publishes price lists that are **averages of real jobs** done by its customers. It explicitly does **not** give final quotes, which can only be obtained from the professionals themselves.
- **What we learn / gap:** Midrag has strong trust signals and general price ranges, but:
  - no **case-specific** range derived from *this* fault's photo;
  - no **contingent quote** structure;
  - no protection against **on-site price changes**;
  - ratings measure satisfaction, not **price reliability**.

## 6. AI "Price-from-Photo" Apps

- **What they are:** A new wave of consumer apps that diagnose a home problem from a photo and estimate the repair cost. Examples include **RepairValueAI** (diagnosis, severity, and a cost range split into DIY vs. professional), **FixIQ** (diagnosis, repair steps and cost estimates), and **Quote Buster** (an AI-generated local price range from a description).
- **Gap:** These are **standalone estimators**. None of the ones we found integrates the estimate into a **real marketplace of competing quotes** or enforces it during the job. Their prices are also typically produced by the AI itself, which we deliberately avoid (see the literature review, §7).

---

## Comparison Matrix

| Feature | Homejoy | TaskRabbit | Thumbtack | Urban Company | Midrag | AI photo apps | **GetHandyMan** |
|---|---|---|---|---|---|---|---|
| Competing quotes | ❌ | ❌ (dropped in 2014) | ✅ | Partial | Off-platform | ❌ | ✅ |
| Fixed prices for standard jobs | ✅ | Hourly | ❌ | ✅ | ❌ | ❌ | ✅ (top scenario) |
| AI diagnosis from photo | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| Case-specific price range | ❌ | ❌ | ❌ | Fixed menu | General price list | ✅ (AI-generated) | ✅ (pricing engine) |
| Contingent, pre-agreed scenarios | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| Change-of-scope protocol | ❌ | ❌ | ❌ | Partial | ❌ | ❌ | ✅ AI-verified |
| Ranking by price reliability | ❌ | ❌ | ❌ | ❌ | ❌ (by rating) | ❌ | ✅ (expected final price) |
| Pro pays only on completed jobs | n/a | n/a (client fees) | ❌ (pay per lead) | Commission | Membership | n/a | ✅ |

---

## Positioning Summary

| Lesson | Source | How GetHandyMan applies it |
|--------|--------|----------------------------|
| Model travel time in matching | Homejoy | Geo + route-aware matching |
| Prevent leakage | Homejoy, Gu & Zhu | Fees only on completed jobs, ₪150 cap, protection only on-platform |
| Raw bids are hard to compare | TaskRabbit | AI reference point + ranking by expected final price |
| Do not charge for leads | Thumbtack | Commission only on completed jobs |
| Hybrid fixed + quote pricing works | Urban Company | Scenario tree combines both |
| Trust signals matter; ranking must not be for sale | Midrag | Pro subscription never buys ranking position |
| Price-from-photo exists but is not enforced | AI apps | Integrate the estimate into the marketplace and the job flow |

---

## Note on the Business-Plan Comparison

The business plan compares our ~13% take rate with a "20% commission" at TaskRabbit. TaskRabbit's current public pages describe a **client-side service fee plus a Trust & Support fee**, both percentages of the hourly rate, after the service fee was **cut from 30% to 15% in 2018**. We recommend rephrasing that comparison as "TaskRabbit charges clients a service fee plus a Trust & Support fee" unless a dated source for 20% is added.

## Sources

- Homejoy: Wikipedia; Fortune and Re/code coverage (July 2015); Backchannel / *Medium* investigation (Oct 2015); TechCrunch (July 2015).
- TaskRabbit: TechCrunch (June 2014, Jan 2015); VentureBeat (July 2014); TaskRabbit press release (June 20, 2018); TaskRabbit support pages on Service and Trust & Support fees.
- Thumbtack: Housecall Pro guide "What is Thumbtack"; Trustpilot, ConsumerAffairs, BBB and Thumbtack community complaints.
- Urban Company: company website; Entrepreneur India; industry business-model analyses.
- Midrag: midrag.co.il, the rating-method page, FAQ, and price-list page.
- AI photo apps: Apple App Store listings (RepairValueAI, FixIQ, Quote Buster).
