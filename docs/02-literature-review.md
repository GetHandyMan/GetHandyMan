# GetHandyMan — Literature Review

**Version:** 0.1 (draft) — November 2026

GetHandyMan rests on six research areas: (1) information asymmetry and credence goods, (2) bidding for incomplete contracts, (3) mechanism design, (4) reputation systems in online platforms, (5) Empirical Bayes estimation, and (6) disintermediation ("leakage") in two-sided marketplaces. We also briefly cover (7) the use of AI models for diagnosis and why we keep them out of pricing. We close with the research gap the project addresses.

---

## 1. Information Asymmetry and Credence Goods

**Akerlof (1970)** [1] showed in *The Market for "Lemons"* that when sellers know more about quality than buyers, markets can unravel: buyers cannot tell good from bad, so they are unwilling to pay for quality.

Home repairs are a sharper case: **credence goods**. Darby and Karni (1973) added this category to Nelson's classification of search and experience goods, with repair services as a typical example [2]. **Dulleck and Kerschbamer (2006)** [2] survey the field in *On Doctors, Mechanics, and Computer Specialists*. They open with the worry we all have when the heating or the washing machine breaks down: paying for a service that was not provided, or receiving unnecessary treatment. They ask under which conditions experts have an incentive to exploit the informational problem, what types of fraud exist (overtreatment, undertreatment, overcharging), and which institutions can deal with it.

Field evidence confirms that the fraud is real and systematic. **Balafoutas, Beck, Kerschbamer and Sutter (2013)** [3] ran a natural field experiment on taxi rides in Athens. Passengers who appeared to have worse information about routes were taken on significantly longer detours, and lack of information about the tariff system raised the likelihood of a manipulated bill by about fifteen percentage points.

**Relevance.** "Bait-and-switch" in home repair is exactly *overcharging* in a credence-goods market. Our scenario tree and change-of-scope protocol are institutional tools of the kind this literature discusses. They make the expert's diagnosis verifiable (documented photos, AI cross-check) and commit prices in advance.

## 2. Bidding for Incomplete Contracts

**Bajari, Houghton and Tadelis (2014)** [4] studied highway-paving procurement. Contracts are awarded to the lowest bidder, but the plans and specifications are often changed after the award, so the final cost differs from the low bid. Using a structural model, they show that **adaptation costs account for 7.5–14% of the winning bid**, and that bidders respond strategically to the expected incompleteness. Markups from private information and market power, the usual focus of auction theory, are much smaller by comparison.

**Relevance.** This is the most direct academic analogue of our problem: a lowest-bid auction plus post-award renegotiation creates room for strategic bidding. Our contribution is to (a) make the contract *less incomplete* through the scenario tree, and (b) price in each bidder's historical tendency to renegotiate through the overrun factor.

## 3. Mechanism Design

Mechanism design asks how to set the rules of a game so that self-interested participants' best response produces the desired outcome. Classic results include **Myerson (1981)** [5] on optimal auction design. In procurement, **Che (1993)** [6] analysed *scoring auctions*, in which bids are evaluated on a score combining price and other attributes instead of price alone.

**Relevance.** Ranking by *quote × overrun factor* is a scoring rule. It turns the auction into one in which the score reflects the *expected final* price, not the announced price. The design aim is incentive compatibility: lowballing should not increase a professional's expected payoff. We will test this property empirically in the simulator rather than prove it in full generality.

## 4. Reputation Systems in Online Platforms

**Resnick, Kuwabara, Zeckhauser and Friedman (2000)** [7] described how reputation systems let strangers trust each other online, and identified their weak points: few ratings, unfair or missing feedback, and the cold-start problem for newcomers. **Dellarocas (2003)** [8] analysed online feedback mechanisms as a form of digitised word of mouth and discussed their design and strategic manipulation. **Tadelis (2016)** [9] reviews feedback systems in online platform markets and notes that ratings tend to be inflated and are often uninformative.

**Relevance.** Star ratings measure *satisfaction*, which a customer may give even after being overcharged. Our overrun factor is an **objective, behaviour-based** reputation signal (final price vs. quote) that the platform measures directly. That makes it harder to inflate.

## 5. Empirical Bayes Estimation

**Efron and Morris (1975)** [10] showed how the James–Stein estimator, viewed in an empirical-Bayes context, improves on raw averages when estimating many related quantities. Their famous example predicts baseball batting averages: each player's early-season average is "shrunk" toward the overall mean, and the shrunken estimates predict the rest of the season much better than the raw averages.

**Relevance.** Estimating each professional's overrun factor is the same problem. A new professional with one job that ran ×2.0 should not be treated as a ×2.0 professional. Empirical Bayes shrinks their estimate toward the category average and moves it away only as evidence accumulates. The prior (mean and variance across professionals) is itself estimated from the platform's data, which is the "empirical" part. This directly addresses the cold-start weakness of reputation systems noted in §4.

## 6. Disintermediation ("Leakage") in Two-Sided Marketplaces

**Gu and Zhu (2021)** [11] ran a randomised controlled trial on an online freelance marketplace. They found that enhanced trust increases the likelihood of high-quality freelancers being hired. However, once trust is high enough, it also increases disintermediation: clients and freelancers go around the platform to avoid its fees, which offsets the revenue gains.

**Relevance.** Leakage is one of the reasons cited for Homejoy's failure (see the competitors review). Our design reduces the incentive to leak in three ways: commission only on completed jobs, a cap of ₪150 per job, and value the platform keeps adding on repeat jobs (the customer protection and change-of-scope arbitration apply only on-platform).

## 7. AI Models for Diagnosis — and Why They Do Not Set Prices

Modern vision–language models can describe a photo and classify a fault zero-shot, and a growing number of consumer apps already estimate repair costs from a photo (see the competitors review). However, large language models are known to **hallucinate**: they produce fluent but unsupported content, including invented numbers. Ji et al. (2023) [12] survey the phenomenon.

**Relevance.** This motivates our strict separation:
- The **AI** outputs only a *structured diagnosis* (category, fault type, confidence), plus follow-up questions when confidence is low. This output can be checked against labels.
- The **pricing engine** computes ranges from a price table and real job data. It is deterministic, auditable and updatable.

---

## 8. Summary and Research Gap

| Area | Key work | What we take | What is missing |
|------|----------|--------------|-----------------|
| Credence goods | Akerlof [1]; Dulleck & Kerschbamer [2]; Balafoutas et al. [3] | Theory and evidence that experts overcharge under information asymmetry | Concrete, deployable mechanisms in a consumer repair app |
| Incomplete contracts | Bajari, Houghton & Tadelis [4] | Low bids + renegotiation create strategic bidding | Applied to highway procurement, not to consumer services |
| Mechanism design | Myerson [5]; Che [6] | Scoring rules can change bidder incentives | No scoring rule based on *historical renegotiation behaviour* |
| Reputation | Resnick et al. [7]; Dellarocas [8]; Tadelis [9] | Reputation enables trust; known weaknesses | Ratings measure satisfaction, not price honesty |
| Empirical Bayes | Efron & Morris [10] | Shrinkage for small-sample estimates | Not applied to pricing reliability of service providers |
| Disintermediation | Gu & Zhu [11] | Trust can increase leakage | Need for a fee design that keeps transactions on-platform |
| AI diagnosis | Ji et al. [12] | LLMs hallucinate numbers | Separation of AI diagnosis from pricing in a marketplace |

**The gap.** Existing work explains *why* lowball-then-renegotiate behaviour happens, and existing platforms use either open bidding or fixed prices. We found no system that combines (a) AI-structured diagnosis, (b) contingent scenario-based quotes, (c) a verified change-of-scope protocol, and (d) a shrinkage-based reliability score used **inside the ranking**, together with a simulation and pilot that measure whether strategic lowballing stops paying.

---

## References

[1] G. A. Akerlof, "The Market for 'Lemons': Quality Uncertainty and the Market Mechanism," *Quarterly Journal of Economics*, vol. 84, no. 3, pp. 488–500, 1970.

[2] U. Dulleck and R. Kerschbamer, "On Doctors, Mechanics, and Computer Specialists: The Economics of Credence Goods," *Journal of Economic Literature*, vol. 44, no. 1, pp. 5–42, 2006. doi:10.1257/002205106776162717

[3] L. Balafoutas, A. Beck, R. Kerschbamer, and M. Sutter, "What Drives Taxi Drivers? A Field Experiment on Fraud in a Market for Credence Goods," *Review of Economic Studies*, vol. 80, no. 3, pp. 876–891, 2013.

[4] P. Bajari, S. Houghton, and S. Tadelis, "Bidding for Incomplete Contracts: An Empirical Analysis of Adaptation Costs," *American Economic Review*, vol. 104, no. 4, pp. 1288–1319, 2014. doi:10.1257/aer.104.4.1288

[5] R. B. Myerson, "Optimal Auction Design," *Mathematics of Operations Research*, vol. 6, no. 1, pp. 58–73, 1981.

[6] Y.-K. Che, "Design Competition Through Multidimensional Auctions," *RAND Journal of Economics*, vol. 24, no. 4, pp. 668–680, 1993.

[7] P. Resnick, K. Kuwabara, R. Zeckhauser, and E. Friedman, "Reputation Systems," *Communications of the ACM*, vol. 43, no. 12, pp. 45–48, 2000.

[8] C. Dellarocas, "The Digitization of Word of Mouth: Promise and Challenges of Online Feedback Mechanisms," *Management Science*, vol. 49, no. 10, pp. 1407–1424, 2003.

[9] S. Tadelis, "Reputation and Feedback Systems in Online Platform Markets," *Annual Review of Economics*, vol. 8, pp. 321–340, 2016.

[10] B. Efron and C. Morris, "Data Analysis Using Stein's Estimator and Its Generalizations," *Journal of the American Statistical Association*, vol. 70, no. 350, pp. 311–319, 1975. doi:10.1080/01621459.1975.10479864

[11] G. Gu and F. Zhu, "Trust and Disintermediation: Evidence from an Online Freelance Marketplace," *Management Science*, vol. 67, no. 2, pp. 794–807, 2021. doi:10.1287/mnsc.2020.3583

[12] Z. Ji et al., "Survey of Hallucination in Natural Language Generation," *ACM Computing Surveys*, vol. 55, no. 12, 2023.
