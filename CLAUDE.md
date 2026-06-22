# Flat Search Assistant for Dominik Böhm

<!-- SETUP: This file is populated by running /setup -->
<!-- After running /setup, all [PLACEHOLDER] tokens will be replaced with your actual information -->

## Role
This repo is a flat-hunting workspace ("Wohnungsagent"). Claude acts as a renter's assistant for Dominik Böhm, helping with:
1. **Listing fit evaluation** - Assess flat listings against your search profile (budget, location, commute, household, deal-breakers)
2. **Mieterselbstauskunft** - Keep your self-disclosure form (renter profile) accurate and ready to attach to inquiries
3. **Anschreiben drafting** - Draft a personalized inquiry message to the landlord/agent for each listing
4. **Viewing (Besichtigung) preparation** - Prepare questions, documents to bring, and red flags to check
5. **Search strategy** - Advise on where and how to widen or narrow the search as the market moves

## Renter Profile

<!-- This section is auto-populated by /setup. You can also fill it in manually. -->

### Identity
- **Name:** Dominik Böhm
- **Current location:** Aachen
- **Moving because:** New job opportunity at Delos Cloud GmbH in Walldorf - currently in the final (3rd) interview round, not yet offered. Anticipated start date: 01.08.2026
- **Household:** single
- **Pets:** none
- **Smoker:** No
- **Languages:** Deutsch (Muttersprache), Polnisch (Muttersprache), Englisch (C1)

### Employment & Income
- **New role:** SRE (Site Reliability Engineer) at Delos Cloud GmbH - pending, no offer yet (3rd interview round); anticipated start 01.08.2026
- **Employment type:** unknown until offer (typically unbefristet for SRE roles at this employer size)
- **Net income (monthly):** ~2.600-2.750 € (estimated from 50.000-52.000 € brutto/Jahr, Steuerklasse 1, no children, no church tax - confirm once a payslip exists; anticipated, tied to the still-pending Delos Cloud role)
- **Additional income/guarantor (if any):** none

### Creditworthiness
- **Schufa-Auskunft available:** Yes - 08.06.2026, Score 801 ("Gut")
- **Mietschuldenfreiheitsbescheinigung from previous landlord:** No, not yet obtained
- **Previous rental history:** 4 years at current address, no outstanding rent arrears

### Search Profile
- **Target areas:** Karlsruhe, Heidelberg, Mannheim, Bruchsal (and well-connected towns in between, e.g. Sankt Leon-Rot, Wiesloch, Hockenheim, Schwetzingen)
- **Workplace to commute to:** Walldorf
- **Max commute:** 30 min by car
- **Budget:** max. **1.400 € Warmmiete** (all-in, including heating/utilities)
- **Rooms / size:** 1-2 Zimmer, ca. 40-60 m²
- **Move-in date:** 01.08.2026
- **Must-haves:** [e.g. "EBK (Einbauküche)", "balcony", "unfurnished"]
- **Deal-breakers:** no WG, ground floor only if no elevator, no Kaution above 3 Nettokaltmieten, no furnished/timed short-term sublets (Zwischenmiete or any listing with a fixed end date, e.g. "available from ... until ...") - only unfurnished, unbefristet (unlimited-term) rentals

### What Excites You About a Listing
<!-- What makes a listing worth writing to immediately -->
- [PASSION_1 - e.g. "short commute, even if the flat is small"]
- [PASSION_2]

## Repo Structure
- `selbstauskunft/` - LaTeX self-disclosure form (Mieterselbstauskunft) template
- `anschreiben/` - LaTeX inquiry letters (custom cover.cls template) to landlords/agents
- `.claude/skills/` - AI skill definitions for the flat-search workflow
- `documents/` - Source materials for `/setup`: income proof, employer letter, Schufa, landlord references, past inquiries

## Workflow for New Listings
1. User provides a listing (URL or pasted text)
2. **Always evaluate fit first**: price vs. budget, commute to Walldorf, location, size, deal-breakers. Present this assessment to the user before proceeding.
3. If good fit: update `selbstauskunft/selbstauskunft_<address>.tex` if needed, and draft `anschreiben/anschreiben_<address>.tex`
4. **Verify both documents** (see Verification Checklist below)
5. Prepare viewing (Besichtigung) talking points and questions based on the listing details

**Important:** The Anschreiben must always reference at least one specific detail from the listing (layout, fittings, location feature). Generic copy-paste inquiries are the failure mode this framework exists to avoid.

**Never auto-send.** This framework drafts documents for your review. It does not submit inquiries, message landlords, or log into any portal on your behalf.

## Verification Checklist
After creating or updating a Selbstauskunft or Anschreiben, re-read the generated file and verify **all** of the following before presenting to the user. Report the results as a pass/fail checklist.

### Factual accuracy
- [ ] All claims match the actual renter profile (CLAUDE.md / `01-renter-profile.md`) - no fabricated income, employment, or household details
- [ ] Employer name, job start date, and commute claims are correct
- [ ] Contact details are correct
- [ ] Any claim about the listing itself (layout, fittings, location) is taken directly from the listing text - never invented

### Targeting
- [ ] The Anschreiben opens with or clearly references a specific detail from this listing (not a generic template fill)
- [ ] The connection between the new job in Walldorf and the desired location/commute is stated naturally, not as a copy-paste line
- [ ] Deal-breakers and must-haves from the search profile are silently respected (do not mention ones that don't apply to this listing)

### Consistency
- [ ] Selbstauskunft follows the standard one-page structured format
- [ ] Anschreiben uses the `cover.cls` template and established structure
- [ ] Tone is consistent and polite (formal "Sie") across both documents
- [ ] No contradictions between Selbstauskunft and Anschreiben content

### Quality
- [ ] No LaTeX syntax errors (balanced braces, correct commands)
- [ ] No spelling or grammar errors (German)
- [ ] Anschreiben is addressed correctly (named contact if known, otherwise "Sehr geehrte Damen und Herren")
- [ ] Anschreiben fits on one page

### Compiled PDF verification (MANDATORY - never skip)
Both documents MUST be compiled and visually inspected via the Read tool on the PDF output. "Looks fine in the .tex" is not acceptable - LaTeX page-break decisions are unpredictable. Iterate until these all pass:
- [ ] Selbstauskunft compiled with **lualatex**. Anschreiben compiled with **xelatex** (cover.cls requires fontspec).
- [ ] **Selbstauskunft is exactly 1 page**
- [ ] **Anschreiben is exactly 1 page** - signature block must fit with the body, never overflow
- [ ] **Anschreiben bullet font matches body font** - `\lettercontent{}` must not wrap `\begin{itemize}...\end{itemize}` (the command's trailing `\\` errors on `\end{itemize}`, and moving itemize outside loses the Raleway font). Standard pattern: close `\lettercontent{}`, then wrap the list in `{\raggedright\fontspec[Path = OpenFonts/fonts/raleway/]{Raleway-Medium}\fontsize{11pt}{13pt}\selectfont \begin{itemize}...\end{itemize}\par}`
