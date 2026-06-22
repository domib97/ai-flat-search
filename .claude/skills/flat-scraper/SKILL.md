---
name: flat-scraper
description: >
  Searches German flat listing sites for new matches against your search profile. Deduplicates across runs.
  Triggers on: flat scrape, find flats, search flats, new listings, Wohnungssuche, scrape flats, /scrape
allowed-tools: Read, Write, Edit, Glob, Grep, WebFetch, WebSearch, Agent, AskUserQuestion
---

# Flat Scraper

---

## How It Works

This skill searches Kleinanzeigen and WG-Gesucht (primary), Immowelt (secondary), and best-effort ImmoScout24, using criteria based on your search profile, deduplicates against previously seen listings and the inquiry tracker, and presents new matches with a quick fit assessment.

**No bot-evasion of any kind.** This skill never tries to defeat Cloudflare, rotate fingerprints, spoof headers, or otherwise circumvent a portal's anti-bot protection. It uses `WebSearch` and plain `WebFetch` only. ImmoScout24 in particular has aggressive anti-bot measures - fetches there will frequently fail, and that is the expected, acceptable outcome rather than something to work around.

## Invocation

The user triggers this skill by saying things like:
- "Find new flats"
- "Scrape for listings"
- "Any new flats in Heidelberg?"
- "/scrape"

Optional arguments:
- A focus area, e.g. "/scrape mannheim" or "/scrape bruchsal"
- "broad" to widen beyond the four named cities into all well-connected towns near Walldorf, e.g. "/scrape broad"

---

## Execution Steps

### Step 0: Load State

1. Read `flat_scraper/seen_listings.json` (create if missing - start with `{"seen": {}}`)
2. Read `flat_search_tracker.csv` to extract already-contacted addresses
3. Read `search-criteria.md` (this directory) for the search strategy and current budget/commute constraints

### Step 1: Search

Run **WebSearch** queries from `search-criteria.md`. By default, run the primary portals (Kleinanzeigen, WG-Gesucht) across all four target cities. If the user said "broad", also include the well-connected towns near Walldorf. If the user specified a focus city, prioritize that city across all portals.

For each search:
- Use `WebSearch` with site-specific queries (`site:kleinanzeigen.de`, `site:wg-gesucht.de`, etc.)
- Also try Immowelt as a secondary source
- Try ImmoScout24 last and treat failed fetches as expected, not an error to retry aggressively
- Look for listings posted in the last 7 days - older listings on these portals are very likely already taken

### Step 2: Fetch & Parse

For each promising result from Step 1:
- Use `WebFetch` to retrieve the listing page (skip if it fails - do not retry repeatedly against the same blocked domain)
- Extract: **address/area**, **Warmmiete or Kaltmiete + Nebenkosten**, **rooms**, **size (m²)**, **posting date** (or "recent"), **URL**, **portal**, **key requirements** (Schufa needed, pets policy, etc.), **availability date**
- Skip if the URL or address+portal combo already exists in `seen_listings.json`
- Skip if the address already appears in `flat_search_tracker.csv`

### Step 3: Quick Fit Assessment

For each new listing, do a rapid fit check (NOT the full evaluation from `04-flat-evaluation.md` - just a quick signal):

- **High match**: Within budget, in or very near a target area, no obvious deal-breakers
- **Medium match**: Within budget but borderline on commute/area, or missing one nice-to-have
- **Low match**: Over budget, outside the practical commute range, or a stated deal-breaker is present

### Step 4: Deduplicate & Store

1. Add ALL fetched listings (new and skipped) to `flat_scraper/seen_listings.json` with structure:
```json
{
  "seen": {
    "<url_or_address_portal_key>": {
      "address": "...",
      "portal": "...",
      "url": "...",
      "warmmiete": 0,
      "first_seen": "YYYY-MM-DD",
      "fit": "high/medium/low",
      "status": "new/skipped/evaluated"
    }
  }
}
```
2. Only present listings NOT already in the seen list or tracker.

### Step 5: Present Results

Present new listings in a table sorted by fit (high first), then by posting recency within each tier - freshness matters as much as fit on these portals since popular listings can disappear within hours:

```
## New Flat Matches - YYYY-MM-DD

Found X new listings (Y high, Z medium, W low match).

| # | Fit | Posted | Address/Area | Warmmiete | Rooms/m² | Portal | URL |
|---|-----|--------|---------------|-----------|----------|--------|-----|
| 1 | High | today | ... | ... € | ... | Kleinanzeigen | [Link](...) |

### High-Match Highlights
For each high-match listing, add 2-3 bullet points:
- Why it matches the search profile
- Estimated commute to Walldorf
- Anything to verify before writing (missing info, ambiguous availability date)
```

After presenting, ask:
> "Want me to evaluate and draft an Anschreiben for any of these? Just give me the number(s)."

If the user picks a number, invoke the **flat-application-assistant** skill workflow (fit evaluation first, then Anschreiben if approved).

### Step 6: Update Tracker (Optional)

If the user decides to write to a landlord about a listing, add a row to `flat_search_tracker.csv`.

---

## Important Rules

1. **Never fabricate listings.** Only present listings found via actual WebSearch/WebFetch results.
2. **Respect deduplication.** Always check `seen_listings.json` AND `flat_search_tracker.csv` before presenting.
3. **Focus on the configured search areas.** Skip listings clearly outside the commute range to Walldorf, unless the user asks to widen the search.
4. **Only currently available listings.** Skip listings explicitly marked as "reserviert" or "nicht mehr verfügbar".
5. **Be efficient with WebFetch.** Don't fetch every search result - use titles and snippets to pre-filter before fetching, and never retry a domain that's clearly blocking automated requests.
6. **No autonomous sending.** This skill only searches and reports. It never contacts a landlord, fills in a portal's inquiry form, or messages anyone - that happens in `/apply` after explicit user approval.

## Optional: Recurring Checks

Because fitting listings can disappear within hours, it can be worth re-running `/scrape` periodically rather than only on demand. Claude Code's `/schedule` or `/loop` skills can run `/scrape` on a timer (e.g. every 30-60 minutes) and report new matches back to you - this only repeats the same WebSearch/WebFetch-based search on a schedule, it does not change what the skill does or add any auto-send capability. Set this up explicitly if you want it; it is not enabled by default.
