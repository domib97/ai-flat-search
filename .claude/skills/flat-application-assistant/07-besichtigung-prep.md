# Besichtigung (Viewing) Preparation Guide

<!-- SETUP: References the renter profile for documents to bring -->

## What to Bring to a Viewing

- Printed (or digital, ready to send on the spot) **Selbstauskunft**
- Copies of the last 3 **Gehaltsabrechnungen** / employment contract for the new job
- **Schufa-Bonitätsauskunft**, if available
- **Mietschuldenfreiheitsbescheinigung** from the previous landlord, if available
- Copy of **Personalausweis** (photo ID) - some landlords ask for this on the spot, never hand over the original
- A notebook or phone to record answers to the questions below

Bringing all documents to the viewing itself (rather than promising to send them later) is a genuine differentiator - it signals the household is organized and ready to sign quickly.

## Questions to Ask During the Viewing

### About Cost
- "Wie setzen sich die Nebenkosten genau zusammen?" (heating, water, building maintenance, etc.)
- "Gab es in den letzten Jahren Nebenkostennachzahlungen?"
- "Wie hoch ist die Kaution, und wie/wann wird sie zurückerstattet?"

### About the Flat Itself
- "Warum zieht der/die aktuelle Mieter/in aus?" (a vague or evasive answer is itself a signal)
- "Wann wurde zuletzt renoviert (Bad, Heizung, Fenster)?"
- "Gibt es Probleme mit Schimmel, Feuchtigkeit oder Lärm?"
- "Ist ein Kellerabteil/Stellplatz/Fahrradraum inklusive?"
- "Gibt es einen Glasfaser- oder Kabelanschluss für Internet?"

### About the Process
- "Bis wann möchten Sie sich entscheiden?"
- "Wie viele Interessenten gibt es bereits?"
- "Welche Unterlagen brauchen Sie zusätzlich zur Selbstauskunft?"
- "Ist der vorgeschlagene Einzugstermin verhandelbar?"

### About the Neighborhood (if not already researched)
- "Wie ist die Verkehrsanbindung Richtung Walldorf / Sankt Leon-Rot / Wiesloch?"
- "Wie ist die Parksituation für Anwohner?"

## Scam Red Flags (take this seriously when relocating from out of town)

Rental scams specifically target people who can't view a flat in person before moving, or who are under time pressure. Watch for:

- **Any request for money before a signed contract or before viewing in person.** A legitimate landlord never asks for a deposit, "reservation fee," or first month's rent via wire transfer, Western Union, gift cards, or crypto before you've seen the flat and signed a Mietvertrag.
- **"Landlord" claims to be abroad and can't show the flat in person**, offering to mail keys after payment. This is one of the most common Kleinanzeigen/WG-Gesucht scam patterns.
- **Price is significantly below market** for the area and size - if a 2-Zimmer-Wohnung in central Heidelberg is listed at 600 € Warmmiete, treat it as a near-certain scam regardless of how genuine the photos look (stolen listing photos are common).
- **Pressure to decide or pay within hours**, citing "many other interested parties," combined with refusal to do a normal viewing.
- **Communication only via WhatsApp/email with broken German or generic copy-paste replies** that don't answer specific questions about the flat.
- **No real address given until after some commitment is made.**

If any of these appear, stop the process and tell the user directly rather than continuing to draft an Anschreiben - flag it as a likely scam before doing anything else.

## After the Viewing (Best Practice)

### Follow-Up
- If genuinely interested, send a short message the same day confirming interest and asking about next steps and timeline.
- If documents weren't all ready at the viewing, send the remaining ones (Schufa, payslips) within 24 hours - speed signals seriousness.

### If Rejected or No Response
- After ~1 week with no response, a brief follow-up asking whether the flat is still available is acceptable.
- Log the outcome in `documents/inquiries/<address-slug>/outcome.md` and in `flat_search_tracker.csv` either way - this is what lets `/setup` calibrate `04-flat-evaluation.md` over time (e.g. if income-strong applications keep losing to others, the issue is more likely competition volume than the profile itself).

## Roleplay Guidelines

When the user asks to practice for an upcoming viewing or call:
1. Ask which listing to simulate (pull details from the tracker or a pasted listing)
2. Play the landlord/agent role: ask the household's situation, income, move-in timeline
3. Include 1-2 harder questions a landlord might ask (e.g. about a short notice period, or why moving from out of town)
4. After each answer, give brief feedback: what was clear, what to tighten
5. End by suggesting which questions from "Questions to Ask During the Viewing" fit this specific listing best
