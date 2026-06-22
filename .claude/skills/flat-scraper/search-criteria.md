# Search Criteria for Flat Scraper

<!-- SETUP: Customize these based on your budget, commute tolerance, and must-haves -->

## Portals

**Primary** (search first, every run):
- **kleinanzeigen.de** - weakest anti-bot protection of the major portals, broad coverage including private landlords
- **wg-gesucht.de** - good coverage for smaller flats and 1-2 Zimmer-Wohnungen, including some non-WG listings

**Secondary** (search if time/budget allows):
- **immowelt.de** - decent agency listing coverage

**Best-effort only** (do not retry aggressively if blocked):
- **immoscout24.de** - largest portal but the strongest anti-bot/Cloudflare protection of the four; treat fetch failures here as expected, not a bug to work around

## Query Categories

Each query should combine a portal site filter with a city/area term and the price ceiling where the site's search syntax supports it.

### Priority 1: Karlsruhe

```
site:kleinanzeigen.de Wohnung Karlsruhe Miete
site:wg-gesucht.de Wohnung Karlsruhe
site:immowelt.de Wohnung Karlsruhe miete
```

### Priority 2: Heidelberg

```
site:kleinanzeigen.de Wohnung Heidelberg Miete
site:wg-gesucht.de Wohnung Heidelberg
site:immowelt.de Wohnung Heidelberg miete
```

### Priority 3: Mannheim

```
site:kleinanzeigen.de Wohnung Mannheim Miete
site:wg-gesucht.de Wohnung Mannheim
site:immowelt.de Wohnung Mannheim miete
```

### Priority 4: Bruchsal (and the Walldorf corridor)

```
site:kleinanzeigen.de Wohnung Bruchsal Miete
site:wg-gesucht.de Wohnung Bruchsal
site:kleinanzeigen.de Wohnung Walldorf OR Sankt Leon-Rot OR Wiesloch OR Hockenheim OR Schwetzingen Miete
```

The corridor towns (Sankt Leon-Rot, Wiesloch, Hockenheim, Schwetzingen, Walldorf itself) are often the best commute-to-price trade-off and should always be included when running "broad" - they are smaller markets with less competition than the four named cities.

## Budget Filter

- **Hard ceiling: 1.400 € Warmmiete** (all-in, including heating/utilities)
- If only Kaltmiete is listed, estimate Warmmiete as Kaltmiete + stated Nebenkosten, or Kaltmiete + ~2-2.50 €/m² if Nebenkosten aren't given - flag the estimate as such
- Listings noticeably above budget can still be surfaced as "Medium/Low match - over budget" if the location/commute is exceptional, but never silently filtered in as if they fit

## Location Filter

- **Target areas:** Karlsruhe, Heidelberg, Mannheim, Bruchsal
- **Always-include corridor towns:** Sankt Leon-Rot, Wiesloch, Hockenheim, Schwetzingen, Walldorf
- **Workplace to commute to:** Walldorf
- **Max commute:** 30 min by car - estimate via WebSearch/WebFetch (Google Maps or VRN/KVV) per listing, do not guess

## Exclude: Furnished Short-Term Sublets (Zwischenmiete)

Dominik needs a permanent, unfurnished, unbefristet (unlimited-term) home for his 01.08.2026 move - not a temporary sublet. **Skip and never present** any listing that:

- Is described as fully furnished - "voll möbliert"/"vollständig möbliert"/"komplett möbliert"/"furnished"/"fully-furnished" (whole-apartment furniture, not just a fitted kitchen), OR
- States a fixed availability window with both a start AND end date (e.g. "verfügbar ab 12.07. bis 07.09.2026", "available from 12th July until 7th September 2026", "Zwischenmiete", "Untermiete", "befristet auf X Monate")

A listing with only a start date ("available from ...", "frei ab ...") and no end date is fine - that is a normal unlimited-term rental. **Do not treat "teilmöbliert" (partially furnished) as a reason to skip** - in this market it almost always just means a fitted kitchen (EBK), which is one of the must-haves, not a short-term sublet signal. The deal-breaker is specifically a stated **end date**, explicit Zwischenmiete/Untermiete/befristet framing, or a listing described as fully/completely furnished.

## Date Filter

Only include listings posted within the last 7 days. These portals move fast - a listing older than a week is often already taken even if still shown as active. If a posting date cannot be determined, include it but flag as "date unknown" and treat it as lower priority than dated listings.

## Adapting Queries

If the user specifies a focus city or town, run all portal queries for that location plus 2-3 custom queries (e.g. a specific street, a specific Stadtteil). For example:
- "/scrape mannheim" -> Priority 3 queries + any Mannheim Stadtteil the user has mentioned as preferred (e.g. Neuostheim, Lindenhof)
