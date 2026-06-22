# Flat Evaluation Framework

<!-- SETUP: Search profile and deal-breakers are personalized by running /setup -->

## Scoring Dimensions

Evaluate each listing against these five dimensions:

### 1. Price Fit (0-100)
Compare the listing's **Warmmiete** (or Kaltmiete + estimated Nebenkosten/Heizkosten if Warmmiete isn't stated) against the 1.400 € budget.

| Score | Meaning |
|-------|---------|
| 80-100 | Warmmiete at or below ~90% of budget - comfortable margin |
| 60-79 | Warmmiete within budget, little to no margin |
| 40-59 | Warmmiete slightly over budget (up to ~5%) - only worth it for a strong commute/location win |
| 0-39 | Clearly over budget |

If only Kaltmiete is listed, estimate Warmmiete as Kaltmiete + Nebenkosten (use the listing's stated Nebenkosten if given, otherwise estimate ~2-2.50 €/m² as a placeholder and flag it as an estimate, not a fact, in the evaluation output.

### 2. Commute Fit to Sankt Leon-Rot (0-100)
- Use WebSearch/WebFetch (e.g. Google Maps, Deutsche Bahn/VRN/KVV journey planner) to estimate door-to-door commute time by car and by public transit from the listing's address to Sankt Leon-Rot.

| Score | Meaning |
|-------|---------|
| 80-100 | Within [YOUR_MAX_COMMUTE], multiple transport options |
| 60-79 | Within [YOUR_MAX_COMMUTE], but only one realistic option (e.g. car only) |
| 40-59 | Slightly over [YOUR_MAX_COMMUTE] |
| 0-39 | Significantly over [YOUR_MAX_COMMUTE] or requires multiple transfers |

Towns directly on the Karlsruhe-Heidelberg-Mannheim corridor or near Sankt Leon-Rot (Walldorf, Wiesloch, Hockenheim, Schwetzingen, Sankt Leon-Rot itself) typically score highest here even if they aren't one of the four named target cities - call this out explicitly when it happens.

### 3. Location & Area Fit (0-100)
- Within target areas (Karlsruhe, Heidelberg, Mannheim, Bruchsal, or a well-connected town in between): PASS baseline
- Outside target areas with poor commute: FAIL (deal-breaker)

| Score | Meaning |
|-------|---------|
| 80-100 | In a target area, good local amenities (groceries, transit stop nearby) |
| 60-79 | In a target area, average amenities |
| 40-59 | Borderline area, acceptable only with a strong commute/price win |
| 0-39 | Outside target areas or known to be a poor area for renters |

### 4. Size, Layout & Must-Haves (Pass/Fail + Notes)
Check the listing against the search profile's must-haves, nice-to-haves, and deal-breakers (`01-renter-profile.md`):
- All must-haves present: PASS
- Missing a must-have: FAIL (deal-breaker) unless the user explicitly wants to consider exceptions
- Any deal-breaker condition present (e.g. no elevator + high floor, smoking flat, WG-only when household isn't a WG): FAIL

### 5. Application Competitiveness (0-100)
How likely is this household to actually get the flat, given how the listing is positioned?

| Score | Meaning |
|-------|---------|
| 80-100 | Income comfortably clears the implied requirement (commonly ~2.5-3x Kaltmiete net), clean Bonität, listing is fresh (posted in the last 24-48h) |
| 60-79 | Meets requirements but listing has likely already drawn many inquiries (posted 3+ days ago on a popular portal) |
| 40-59 | Meets requirements on paper but something is a soft negative (e.g. short notice before move-in, no Schufa yet) |
| 0-39 | Unlikely to be competitive - income below typical multiple, or listing explicitly states criteria the household doesn't meet |

**Listing freshness matters as much as fit.** A 90/100 fit posted 5 days ago on a popular portal may already be gone; a 70/100 fit posted 1 hour ago is often the better use of time. Always note posting age/recency in the evaluation.

## Output Format

Present the evaluation as:

```
## Flat Fit Evaluation: [Address/short description] - [Portal]

| Dimension | Score | Notes |
|-----------|-------|-------|
| Price Fit | XX/100 | [Warmmiete vs budget, note if estimated] |
| Commute Fit | XX/100 | [commute time by car/transit to Sankt Leon-Rot] |
| Location & Area | XX/100 | [brief note] |
| Size/Layout/Must-haves | PASS/FAIL | [which must-haves met/missing] |
| Application Competitiveness | XX/100 | [posting age, income multiple] |

**Overall Score: XX/100** (weighted average of scored dimensions)

### Verdict: [Strong Fit / Good Fit / Moderate Fit / Weak Fit / Poor Fit]

### Why It's Worth Writing To (or Not)
- [bullet points]

### Open Questions for the Anschreiben or Viewing
- [bullet points - things the listing doesn't make clear: exact Nebenkosten, pets policy, available date]

### Recommendation
[1-2 sentences: write today / write but expect a long shot / skip]
```

## Weighting
- Price Fit: 25%
- Commute Fit: 30%
- Location & Area: 15%
- Application Competitiveness: 30%

(Size/Layout/Must-haves is pass/fail, not weighted - a missing must-have or present deal-breaker overrides the score and should be surfaced as a hard stop regardless of the weighted total.)

## Thresholds
- **Strong Fit** (75+): Write today, ideally within the hour for fresh listings
- **Good Fit** (60-74): Write within 24h
- **Moderate Fit** (45-59): Worth writing if the search is otherwise slow; discuss with user first
- **Weak Fit** (30-44): Skip unless nothing better is available
- **Poor Fit** (<30): Skip

## Before Writing: Should You Call or Message First?

For listings that move fast (posted within the last few hours, popular area, clearly under-priced for the area), consider suggesting a same-day phone call or WhatsApp message **in addition to** the written Anschreiben, rather than relying on a portal message that may sit in an inbox behind dozens of others.

### When to Suggest Calling/Messaging Directly
- A phone number is listed and the posting is less than ~24h old
- The listing explicitly invites calls ("Bei Interesse bitte anrufen")
- The ad is ambiguous about availability or move-in date and a quick question would clarify before investing time in a full Anschreiben

### Good Questions for a Quick Call
- "Ist die Wohnung noch verfügbar?"
- "Wäre ein Besichtigungstermin diese Woche möglich?"
- "Gibt es bereits viele Interessenten?"

Keep the call short - the goal is to secure a viewing slot, not to deliver the full pitch. Send the written Anschreiben (with Selbstauskunft attached) as a follow-up regardless of whether the call happens.
