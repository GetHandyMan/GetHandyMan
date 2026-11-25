# GetHandyMan — Clickable Prototype (5 screens)

**Team:** Idan Lachovitz, Idan Pekler · November 2026

A clickable, Hebrew (RTL) mobile prototype at 390×844 that walks through the full flow, from photo to fair final price. The screens are linked, and the key controls work: sort toggles, quote selection, the customer's decision buttons, and live price inputs on the professional's side.

All five screens use the same worked example, a leaking sink, with one consistent set of numbers:

| Scenario | Probability | Market range |
|----------|-------------|--------------|
| Seal replacement / tightening | 60% | ₪150–250 |
| Siphon (trap) replacement | 30% | ₪250–450 |
| Pipe damage inside the wall | 10% | ₪800–2,000 |

Expected market price = 0.6·200 + 0.3·350 + 0.1·1,400 ≈ **₪365**.

## Screens

### 1. AI diagnosis and scenario tree (customer)
- Up to 5 photos plus a voice note with automatic transcription.
- An AI diagnosis card showing category, fault type ("leak from sink siphon") and a confidence bar (72%).
- Because confidence is below the threshold, the AI asks a **follow-up question**: "take a close-up of the siphon–wall connection".
- **Scenario tree:** each scenario shows its probability bar and market range, plus the probability-weighted expected price.
- CTA: "Send to 5 professionals nearby".

### 2. Quote comparison with a fairness meter (customer)
- Four contingent quotes, each with a price for every scenario.
- A **toggle between "expected final price" and "quoted price"**. It shows live how the ranking changes:
  - Ranked by quoted price, the lowballer (₪150 base, historically ×1.8) comes first.
  - Ranked by expected final price, he drops to last.
- A **fairness meter** on each card shows where the expected price sits relative to the fair market band, coloured green, amber or red.
- A "Fair pricer" badge for professionals with a low overrun factor.
- Expected final price = Σ p(scenario) · price(scenario) × overrun factor.

### 3. Live tracking (customer)
- A map with the route and ETA, plus the professional's card with their overrun factor ("finishes on average ×1.05 of the quote").
- **The signed agreement:** the price of every scenario, locked in advance. Any scenario outside the list requires documentation and the customer's approval.
- A progress bar: ordered → on the way → working → done.

### 4. Change-of-scope approval (customer), the core of the solution
- The professional's in-app evidence (photos and video, timestamped) and their explanation.
- **AI verification checklist:**
  - ✓ the evidence shows the claimed problem
  - ✓ the problem was not visible in the original photos
  - ✓ the scenario already exists in the agreement
- The price change ₪350 → ₪1,200, marked as **identical to the price committed in advance**.
- Three decisions, all of which work in the prototype: **Approve and continue**, **Stop and pay the visit fee only (₪100)**, or **Request a second opinion**.

### 5. Contingent quote (professional)
- A dark "pro mode" with the incoming request (diagnosis, distance, desired time) and a response countdown.
- A price input for **every scenario plus a visit fee**, each with its market range and a **real-time status**: in range, above range, significantly above, or below market.
- A live preview of **what the customer will see**: the expected final price after the pro's overrun factor. Copy on the screen explains that a deliberately low quote will not improve the ranking.

## Design notes
- **Typeface:** Rubik (Hebrew + Latin). **Palette:** off-white ground and a single teal accent; green, amber and red are reserved for fairness status.
- **Accessibility:** real buttons, links and labelled inputs; touch targets ≥ 44 px.
- A share link to the interactive prototype will be added here.
