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

### Priority 4: Bruchsal (and the Sankt Leon-Rot corridor)

```
site:kleinanzeigen.de Wohnung Bruchsal Miete
site:wg-gesucht.de Wohnung Bruchsal
site:kleinanzeigen.de Wohnung Walldorf OR Wiesloch OR Hockenheim OR Schwetzingen Miete
```

The corridor towns (Walldorf, Wiesloch, Hockenheim, Schwetzingen, Sankt Leon-Rot itself) are often the best commute-to-price trade-off and should always be included when running "broad" - they are smaller markets with less competition than the four named cities.

## Budget Filter

- **Hard ceiling: 1.400 € Warmmiete** (all-in, including heating/utilities)
- If only Kaltmiete is listed, estimate Warmmiete as Kaltmiete + stated Nebenkosten, or Kaltmiete + ~2-2.50 €/m² if Nebenkosten aren't given - flag the estimate as such
- Listings noticeably above budget can still be surfaced as "Medium/Low match - over budget" if the location/commute is exceptional, but never silently filtered in as if they fit

## Location Filter

- **Target areas:** Karlsruhe, Heidelberg, Mannheim, Bruchsal
- **Always-include corridor towns:** Walldorf, Wiesloch, Hockenheim, Schwetzingen, Sankt Leon-Rot
- **Workplace to commute to:** Sankt Leon-Rot
- **Max commute:** [YOUR_MAX_COMMUTE] - estimate via WebSearch/WebFetch (Google Maps or VRN/KVV) per listing, do not guess

## Date Filter

Only include listings posted within the last 7 days. These portals move fast - a listing older than a week is often already taken even if still shown as active. If a posting date cannot be determined, include it but flag as "date unknown" and treat it as lower priority than dated listings.

## Adapting Queries

If the user specifies a focus city or town, run all portal queries for that location plus 2-3 custom queries (e.g. a specific street, a specific Stadtteil). For example:
- "/scrape mannheim" -> Priority 3 queries + any Mannheim Stadtteil the user has mentioned as preferred (e.g. Neuostheim, Lindenhof)
