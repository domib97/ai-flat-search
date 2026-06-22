# /setup - Renter Profile Onboarding

You are running the onboarding setup for the AI Flat Search framework. Your goal is to collect the user's renter information and populate all profile files so the `/scrape` and `/apply` workflows work out of the box.

There are three paths into setup. Step 0 picks the right one; all three converge on Step 3 (file generation) and Step 4 (confirmation).

The search criteria (target areas: Karlsruhe, Heidelberg, Mannheim, Bruchsal; budget: 1.400 € Warmmiete; workplace: Walldorf; primary portals: Kleinanzeigen, WG-Gesucht) are already baked into `CLAUDE.md` and `search-criteria.md` as defaults. Setup only needs to confirm or adjust them, plus collect the personal/financial details that can't be guessed.

---

## Step 0: Welcome & Choose Path

If `$ARGUMENTS` contains `--section <name>`, skip directly to that section in Path C for an update-only flow. Do not run the path-selection prompt below.

Otherwise, before greeting the user, scan the `documents/` folder. Use Glob with `documents/**/*` and count files per subfolder (`income/`, `employer/`, `credit/`, `landlord_refs/`, `inquiries/`).

Then welcome the user with a single message that lists three paths. The wording changes based on what was found.

**If `documents/` has files** in one or more subfolders, lead with Path A:

> **Welcome to the AI Flat Search setup!**
>
> I'll help you build your renter profile so Claude can evaluate listings, keep your Mieterselbstauskunft current, write Anschreiben, and prep you for viewings.
>
> I see files in your `documents/` folder: [list per subfolder, e.g. "2 in income/, 1 in employer/"]. Three ways to start:
>
> **Path A: Read my documents folder** (recommended for what you have) - I'll read everything in `documents/` and build your profile from real source materials. Idempotent and safe to re-run as you add more documents.
>
> **Path B: Paste your details** - Paste your key facts (income, employer, household) here. I'll extract them and ask follow-up questions for what's missing.
>
> **Path C: Interview mode** - I'll walk you through structured questions section by section.
>
> Which would you like?

**If `documents/` is empty or missing**, surface Path A as a "do this if you have materials" option:

> **Welcome to the AI Flat Search setup!**
>
> I'll help you build your renter profile so Claude can evaluate listings, keep your Mieterselbstauskunft current, write Anschreiben, and prep you for viewings.
>
> Three ways to start:
>
> **Path A: Documents folder** (best signal if you have several materials) - Drop your payslips, employment contract, Schufa, and any previous landlord reference in the `documents/` folder, then say "go". See `documents/README.md` for the folder layout.
>
> **Path B: Paste your details** - Tell me your income, employer, household, and move-in date directly in chat.
>
> **Path C: Interview mode** - I'll walk you through structured questions section by section. Good if you're starting from scratch.
>
> Which would you like?

Wait for the user's choice. If they pick A but the folder is still empty, tell them what to add (point at `documents/README.md`) and stop.

---

## Path A: Documents Folder

Reads documents in `documents/`, cross-references them for consistency, and merges extracted data into the renter profile skill files. Read-before-write and idempotent: changes already present will not be proposed again.

### Step A1: Inventory

Use Glob with `documents/**/*` to scan the full tree. Print:

```
## Documents Found

**income/**: [list files, or "(empty)"]
**employer/**: [list files, or "(empty)"]
**credit/**: [list files, or "(empty)"]
**landlord_refs/**: [list files, or "(empty)"]
**inquiries/**: [list subfolders with their files, or "(empty)"]

I will read these before proposing any changes.
```

If every subfolder is empty, stop and tell the user to populate the folder. Point at `documents/README.md`.

### Step A2: Read Existing Skill Files

Read these in parallel before extracting anything:
- `.claude/skills/flat-application-assistant/01-renter-profile.md`
- `.claude/skills/flat-application-assistant/02-tenant-profile.md`

Hold this content in context throughout Path A. Do not re-read.

### Step A3: Parse Documents

**`income/` documents:** net income figures, employer name, contract type and dates, payslip period.

**`employer/` documents:** new job title, employer name, start date, Walldorf work location, contract type (Probezeit, unbefristet).

**`credit/` documents:** Schufa-Bonitätsauskunft date and score/summary if visible.

**`landlord_refs/` documents:** previous landlord name, tenancy dates, any statement of no arrears (Mietschuldenfreiheitsbescheinigung).

**`inquiries/<address-slug>/` subfolders:**
- `listing.md`: the original listing text
- `anschreiben.tex`: the Anschreiben sent
- `outcome.md`: result (viewing offered / rejected / no response / signed) and notes

### Step A4: Cross-Reference Check

Check for inconsistencies before mapping anything:
- Income figures that disagree across payslips
- Employer name spelled differently across documents
- Job start date mismatches between contract and other documents

If inconsistencies are found, present them as a numbered list and wait for the user to resolve each one. If none, state "No cross-reference issues found." and continue.

### Step A5: Build Change Sets

For `01-renter-profile.md` and `02-tenant-profile.md`, compare extracted document content against current file content from Step A2.

**Additive:** new content not in the file in any form (e.g. a Schufa date not yet recorded, a landlord reference not yet listed).

**Conflicting:** content that disagrees with something already recorded (e.g. a different net income figure, a different job start date).

### Step A6: Present and Confirm Changes

Present the full change set before writing anything, grouped by target file. Then ask:

> **Apply all additive changes?** Reply **yes** to apply all, or list the numbers you want to skip.

For conflicts, present one at a time with `[keep]` / `[replace]` / `[manual]` options, same pattern as additive changes. If no conflicts, state "No conflicting changes found."

### Step A7: Write Confirmed Changes and Fill Gaps

Apply confirmed changes with the Edit tool - targeted edits only, do not rewrite entire files.

Documents cover income, employment, and credit. They do not cover everything `/apply` needs. After the writes, ask follow-up questions for gaps:
- Household composition, pets, smoker status
- Desired move-in date
- Must-haves, nice-to-haves, deal-breakers
- Confirm the search defaults: target areas (Karlsruhe, Heidelberg, Mannheim, Bruchsal + corridor towns), max commute to Walldorf, 1.400 € Warmmiete budget, portal priority (Kleinanzeigen + WG-Gesucht primary) - ask if any of these should change rather than re-deriving them from scratch

Then proceed to Step 3.

---

## Path B: Paste Your Details

If the user pastes their details directly (income, employer, household, move-in date):

1. Extract all structured information.
2. Present a summary of what was extracted.
3. Ask follow-up questions for gaps (creditworthiness, deal-breakers, search criteria confirmation).
4. Proceed to Step 3.

---

## Path C: Interview Mode

Walk through each section conversationally, not as a form.

### Section 1: Identity & Household
- Full name, current address, phone, email
- Household composition, pets, smoker status
- Languages

### Section 2: Employment & Income
- New job title, employer, start date, Walldorf as the work location
- Contract type (unbefristet/befristet, Probezeit)
- Net monthly income
- Previous job (for continuity in the Selbstauskunft)
- Any guarantor or additional income

### Section 3: Creditworthiness
- Whether a Schufa-Bonitätsauskunft is already available, or needs to be requested
- Whether a Mietschuldenfreiheitsbescheinigung from the current landlord is available
- Years at current address, any rent arrears (should be none)

### Section 4: Search Configuration
This generates/confirms the criteria that power `/scrape`. The defaults below are already known from the initial request - confirm them rather than asking from scratch, and only probe deeper on what's genuinely undecided:

- **Target areas:** Karlsruhe, Heidelberg, Mannheim, Bruchsal - confirm, or ask if corridor towns (Sankt Leon-Rot, Wiesloch, Hockenheim, Schwetzingen, Walldorf) should be included by default or only under "/scrape broad"
- **Budget:** 1.400 € Warmmiete - confirm this is still accurate
- **Max commute to Walldorf:** ask for a concrete number (e.g. "30 min by car") - this is not yet known and is needed for `04-flat-evaluation.md` and `search-criteria.md`
- **Rooms / size:** ask for a preference
- **Move-in date:** ask for the target date (tie to the new job's start date)
- **Must-haves / deal-breakers:** ask directly
- **Portals:** Kleinanzeigen + WG-Gesucht primary, Immowelt secondary, ImmoScout24 best-effort - confirm or ask if the user wants a different priority

---

## Step 3: Generate Profile Files

Once data collection is complete, populate the following. **For Path A**, the two skill files are already populated by Step A7; check each before writing and skip if its content is no longer placeholder text.

### 1. Update `CLAUDE.md`
Replace all `[PLACEHOLDER]` tokens with the user's actual information. Keep the structure, workflow, and verification checklist intact.

### 2. Populate `01-renter-profile.md` *(Path B and C; skip if Path A populated it)*
Write the full renter profile: identity, household, employment & income, creditworthiness, search profile, references.

### 3. Populate `02-tenant-profile.md` *(Path B and C; skip if Path A populated it)*
Write the tenant persona based on the collected facts - selling points, how the household presents, things to address proactively.

### 4. Update `.claude/skills/flat-application-assistant/04-flat-evaluation.md`
Fill in `[YOUR_MAX_COMMUTE]` with the actual commute tolerance.

### 5. Update `selbstauskunft/selbstauskunft_example.tex`
Replace placeholder personal, employment, and income data with the user's actual details.

### 6. Update `.claude/skills/flat-scraper/search-criteria.md`
Fill in `[YOUR_MAX_COMMUTE]`. Adjust portal priority or location list only if the user asked for a change from the defaults.

---

## Step 4: Confirm & Next Steps

Present a summary:

> **Setup complete!** Here's what was generated:
>
> - `CLAUDE.md` - Your full renter profile
> - `.claude/skills/flat-application-assistant/01-renter-profile.md` - Structured profile
> - `.claude/skills/flat-application-assistant/02-tenant-profile.md` - Tenant persona
> - `.claude/skills/flat-application-assistant/04-flat-evaluation.md` - Commute tolerance filled in
> - `selbstauskunft/selbstauskunft_example.tex` - Your Mieterselbstauskunft
> - `.claude/skills/flat-scraper/search-criteria.md` - Search criteria for `/scrape`
>
> **Try it out:**
> - Run `/scrape` to search for matching listings right now
> - Run `/apply` with a listing URL or pasted text to see the full inquiry workflow
> - Run `/setup --section search` later to update your search criteria as your priorities evolve

---

## Design Principles

- Three onboarding paths converge on the same skill files. Step 0 picks the right path based on what's in `documents/`.
- Path A is read-before-write and idempotent. Conflicts are surfaced for explicit resolution, never silently overwritten.
- Search criteria (areas, budget, workplace) start pre-filled from the original request - setup confirms and fills the gap (commute tolerance), it does not re-derive what's already known.
- Can be re-run with `--section <name>` to update specific sections (e.g. `/setup --section search`).
- At the end, suggest running `/scrape` and `/apply` with a test listing.
