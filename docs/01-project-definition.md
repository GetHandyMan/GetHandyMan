# GetHandyMan — Project Definition

**Final Project, B.Sc. Computer Science — Afeka College of Engineering**
**Supervisor:** Dr. Yair Even-Zohar
**Team:** Idan Lachovitz, Idan Pekler
**Version:** 0.1 (draft) — November 2026

---

## 1. Project Summary

GetHandyMan is a "Gett-style" marketplace app for home-repair professionals: handymen, plumbers, electricians and air-conditioning technicians. A customer photographs a fault and describes it in text or a voice recording. Nearby professionals receive the request and send price quotes, and the customer chooses one.

The innovation is an **AI and algorithmic mechanism that keeps prices fair**. It is designed to prevent professionals from "lowballing" a quote to win the job and then raising the price on site.

## 2. Problem Statement

A professional quotes, for example, ₪200 to fix a leaking sink. On site the problem turns out to be more complex, and the price jumps. From the outside, two very different situations look identical:

| Case | Description | Desired outcome |
|------|-------------|-----------------|
| **1. Legitimate change** | Hidden damage that could not be seen in the photo, e.g. a rotten pipe inside the wall | Allow it, fairly and transparently |
| **2. Bait-and-switch** | A deliberately low quote to win the job, then "discovering" a problem on site when the customer is already under pressure | Detect it and penalise it |

This is a classic **information-asymmetry** problem. Home repairs are *credence goods*: the customer cannot judge which repair is actually needed, even after it has been done. Existing platforms either ignore the problem (open bidding) or avoid it by fixing prices, which does not work for non-standard jobs.

**Goal:** enable case 1 fairly, and detect and penalise case 2.

## 3. The Solution: Four Components

### 3.1 The AI diagnoses; it does not price
A vision–language model analyses the photo and the text or voice description and returns **structured data**: category, fault type, and confidence level. If confidence is low, it asks the customer a follow-up question, e.g. "Please take a close-up photo of the connection."

The LLM **does not set the price**, because language models are known to invent numbers. Prices come from our own **pricing engine**, which is based on a price table and real historical job data.

### 3.2 Contingent quote (scenario tree)
For each diagnosis the system builds the set of plausible scenarios with probabilities. The professional prices **every scenario in advance**. Example for a leaking sink:

| Scenario | Probability | Market range |
|----------|-------------|--------------|
| Seal replacement / tightening | 60% | ₪150–250 |
| Siphon (trap) replacement | 30% | ₪250–450 |
| Pipe damage inside the wall | 10% | ₪800–2,000 |

If scenario 3 turns out to be the real one, its price has **already been agreed**, so there is no surprise.

### 3.3 Change-of-scope protocol
If something is discovered that is not covered by the agreement:
1. The professional must document it **in the app** with a photo or video.
2. The AI checks that the documentation really shows the problem, and that the problem **was not already visible in the original photos**. If it was visible, the professional should have priced it in advance, and the event is **recorded against them**.
3. The customer decides: **approve**, **stop and pay only the visit fee**, or **request a second opinion**.
4. Work does not continue without the customer's approval.

### 3.4 Ranking by "expected final price" (the algorithmic core)
Each professional gets an **overrun factor**: how much higher, on average, their final price is than their quote. It is estimated with a **Bayesian (Empirical Bayes)** model, so a new professional is not penalised for a single job that went wrong.

Quotes are ranked by:

> **Expected price = Quote × Overrun factor**

- Danny quotes ₪150 but historically finishes at ×1.8, so his expected price is ₪270 and he ranks **low**.
- Yossi quotes ₪200 and finishes at ×1.05, so his expected price is ₪210 and he ranks **high**.

**Result:** quoting low on purpose no longer pays. In game-theory terms this is **mechanism design**: the rules are built so that the professional's best strategy is to be honest.

## 4. Goals and Success Criteria

| # | Goal | Measure of success |
|---|------|--------------------|
| G1 | Accurate AI diagnosis from photo + text/voice | Top-1 / top-3 accuracy of category and fault type on a labelled dataset |
| G2 | Reliable price ranges | % of real final prices that fall inside the predicted range (pilot target: ~85%) |
| G3 | Bait-and-switch stops paying | In simulation, strategic (lowballing) professionals lose market share over time under our ranking, but not under a "lowest quote wins" baseline |
| G4 | Fair treatment of legitimate changes | Hidden-damage changes, approved and documented, do not hurt honest professionals' rankings significantly |
| G5 | Working product | Two-role mobile app (customer / professional) supporting the full flow end to end |
| G6 | Real-world validation | Two-week pilot with 10–20 real professionals and real customers |

## 5. Target Users

- **Customers:** homeowners and tenants who need a repair and fear being overcharged.
- **Professionals:** independent handymen, plumbers, electricians and AC technicians who want fair access to jobs without paying for leads that do not convert.
- **Business customers (later):** property-management companies.

## 6. Scope

### In scope
- A mobile app with two roles (customer and professional).
- Fault capture: photos, text, voice recording.
- AI diagnosis service (structured output + follow-up questions).
- Pricing engine and scenario-tree generation.
- Geographic matching to nearby professionals.
- Contingent quotes, comparison screen, and ranking by expected final price.
- Job flow: selection, digital sign-off, live tracking, change-of-scope protocol, payment (simulated), and rating.
- Overrun-factor model (Empirical Bayes) and learning loop that updates scenario probabilities and reliability scores.
- An agent-based simulator, a labelled dataset, and a pilot.

### Out of scope
- Real payment processing (payments are simulated).
- Licensing and insurance verification of professionals beyond basic manual onboarding.
- Areas outside Tel Aviv / Ramat Gan, and trades beyond the four initial ones.
- Training a vision model from scratch; we use existing models, with prompting and possibly fine-tuning.

## 7. User Journey

```
Photo → AI diagnosis → Scenario tree → Sent to 5 nearby pros → Contingent quotes
→ Ranking by expected price → Selection + digital signature → Live tracking
→ (Documented & approved change of scope) → Payment → Rating
→ System learns: updates scenario probabilities and reliability scores
```

## 8. Proving It Works (Academic Part)

1. **Simulator:** a map of Tel Aviv with virtual professionals. Some are honest and some are strategic, i.e. they quote low and raise the price on site. We show in a graph that in a regular system the strategic ones win, while in our system they lose jobs over time.
2. **Dataset:** several hundred labelled faults with real prices. We measure diagnosis accuracy and the percentage of real prices that fall within the predicted range.
3. **Pilot:** 10–20 real professionals (acquaintances, family) and real customers for two weeks. The target outcome is a statement such as *"85% of quotes fell within the range the system predicted."*

## 9. Business Model (team assumptions, from the business plan)

| Revenue stream | Price |
|----------------|-------|
| Commission from the professional, **only on completed jobs** | 10%, capped at ₪150 per job |
| Pro subscription | ₪149/month: commission reduced to 6% + extra tools. **Does not buy ranking position.** |
| Customer protection fee | ₪9.90 per job |
| Business subscription (property managers) | ₪299/month |

*Example:* on a ₪350 job the platform earns about ₪45 (₪35 commission + ₪9.90 fee), roughly 13%.

*Year-3 projection (working assumptions):* 60,000 jobs/year, about ₪3.7M revenue, break-even early in year 4.
*Go-to-market:* start in Tel Aviv and Ramat Gan, four trades, 30–50 manually recruited professionals, 0% commission at launch.

## 10. Recommended Technology

| Layer | Choice |
|-------|--------|
| Mobile app | Flutter or React Native (one app, two roles) |
| Backend | FastAPI (Python) or Node.js |
| Database | PostgreSQL + PostGIS (geographic search) |
| Real-time | WebSockets (quotes, tracking, change-of-scope approvals) |
| AI | Vision–language model for diagnosis (structured JSON output) |
| Payments | Simulated |

## 11. Team and Work Division (proposed)

| Area | Responsibilities |
|------|------------------|
| App & design | Mobile app, UX/UI, the two roles |
| Backend & infrastructure | API, database, geo-matching, real-time, deployment |
| AI, algorithms & simulator | Diagnosis service, pricing engine, scenario tree, Empirical Bayes model, simulator |
| Everyone | Research, dataset, pilot |

## 12. What Has Already Been Prepared

- A full business plan: market, competitors, business model, pricing and 3-year projection. The risks and references chapters are still to be written.
- Five clickable app screens: AI diagnosis; quote comparison with a fairness meter and a "expected price" vs. "quoted price" toggle; live tracking; change-of-scope approval; and the professional's screen for pricing each scenario with a real-time in-range indicator.

## 13. One-Sentence Pitch

> *"We treat mid-job price changes as an information-asymmetry problem. Our solution combines scenario-based contingent quotes, a documented change-of-scope protocol with AI verification, and a Bayesian model of pricing reliability. We use it to rank quotes by expected final price, so a deliberately low quote stops paying off. We will prove it with a simulation and a pilot."*
