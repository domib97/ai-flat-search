---
name: flat-application-assistant
description: >
  Assists with flat inquiries: evaluating listings, keeping the Mieterselbstauskunft current, writing
  Anschreiben to landlords/agents, and preparing for viewings (Besichtigungen). Triggers on keywords like:
  flat listing, Wohnung, Inserat, Mieterselbstauskunft, Anschreiben, Besichtigung, viewing, apartment,
  apply for flat, rental inquiry, Vermieter, Kaltmiete, Warmmiete.
allowed-tools: Read, Glob, Grep, WebFetch, WebSearch, Edit, Write, AskUserQuestion
---

# Flat Application Assistant

---

## Workflow

When the user provides a listing (URL or text), follow this workflow:

### Step 1: Research & Evaluate Fit
- Fetch the listing content (use WebFetch for URLs; if the portal blocks fetching, ask the user to paste the listing text)
- Extract: address/area, Warmmiete (or Kaltmiete + Nebenkosten), rooms, size, availability date, required documents, contact channel
- Estimate commute to Sankt Leon-Rot via WebSearch/WebFetch (Google Maps, VRN/KVV journey planner)
- Score the listing against the search profile using the framework in `04-flat-evaluation.md`
- Present the evaluation table and verdict
- Flag scam red flags immediately if present (see `07-besichtigung-prep.md`) and stop the workflow if so
- Ask the user if they want to proceed with an Anschreiben

### Step 2: Keep the Selbstauskunft Current
- Read the existing Selbstauskunft (`selbstauskunft/selbstauskunft_example.tex` or a previous listing-specific copy) as reference
- Follow the guidelines in `05-selbstauskunft-templates.md`
- Only Section 5 (move-in date, sometimes household framing) should change per listing - everything else must stay identical to `01-renter-profile.md`

### Step 3: Write the Anschreiben
- Follow the writing style rules in `03-writing-style.md` (critical: no em-dashes, no rental clichés, reference a specific listing detail)
- Follow the template structure in `06-anschreiben-templates.md`
- Create `anschreiben/anschreiben_<address-slug>.tex`
- Ensure the letter connects the household's situation (new job in Sankt Leon-Rot) to this specific listing

### Step 4: Viewing Preparation
- Follow the framework in `07-besichtigung-prep.md`
- List documents to bring
- Prepare questions to ask the landlord/agent
- Re-check for scam red flags one more time before the user attends

---

## Reference Files

| File | Purpose |
|------|---------|
| `01-renter-profile.md` | Identity, household, employment, income, creditworthiness, search profile |
| `02-tenant-profile.md` | Tenant persona, selling points, how to present consistently |
| `03-writing-style.md` | Tone, structure, do's and don'ts for Anschreiben |
| `04-flat-evaluation.md` | Scoring framework for listing fit |
| `05-selbstauskunft-templates.md` | LaTeX Selbstauskunft structure and tailoring rules |
| `06-anschreiben-templates.md` | LaTeX Anschreiben structure and tailoring rules |
| `07-besichtigung-prep.md` | Documents to bring, questions to ask, scam red flags, follow-up |

---

## Quick Commands

The user may also ask for individual steps without the full workflow:
- "Evaluate this listing" - Step 1 only
- "Update my Selbstauskunft" - Step 2 only
- "Write an Anschreiben for [address]" - Step 3 only
- "Help me prepare for the viewing at [address]" - Step 4 only
- "Is this listing a scam?" - quick check against `07-besichtigung-prep.md` red flags only

## Hard Rule: Draft Only, Never Send

This skill drafts documents for the user's review. It never sends a message, submits a portal inquiry form, or logs into any portal on the user's behalf.
