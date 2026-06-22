# /reset - Reset Renter Profile Data

You are resetting parts of the flat search framework back to a blank state so the user can start fresh with `/setup`.

**This command is destructive.** Nothing is deleted until the user explicitly confirms. Follow these steps exactly in order.

---

## Step 0: Parse Scope from Arguments

Check `$ARGUMENTS` for a scope keyword:

- `profile` — clears renter profile data from skill files only
- `documents` — deletes user-provided files from the `documents/` folder only
- `all` — both of the above

If `$ARGUMENTS` is empty or does not contain a recognized scope keyword, ask:

> **What would you like to reset?**
>
> - **`profile`** — Clears your renter and tenant profile data (identity, household, income, search criteria specifics). The search defaults (target areas, budget, workplace) and framework structure are preserved.
>
> - **`documents`** — Deletes all files you've placed in the `documents/` folder (income proof, employer letter, Schufa, landlord references, past inquiries). The folder structure and `README.md` are preserved.
>
> - **`all`** — Both of the above.
>
> Reply with `profile`, `documents`, or `all`.

Wait for the user's response before continuing.

---

## Step 1: Show Exactly What Will Be Cleared

### If scope includes `profile`:

Read the current state of these files and report whether each has content or is already a blank/placeholder template:

- `.claude/skills/flat-application-assistant/01-renter-profile.md`
- `.claude/skills/flat-application-assistant/02-tenant-profile.md`
- `.claude/skills/flat-application-assistant/04-flat-evaluation.md` *(the `[YOUR_MAX_COMMUTE]` placeholder only — scoring framework and weighting are preserved)*
- `.claude/skills/flat-scraper/search-criteria.md` *(the `[YOUR_MAX_COMMUTE]` placeholder only — portal list and query structure are preserved)*
- `CLAUDE.md` *(renter profile section only — workflow and verification checklist are preserved)*

Present as:

```
## Profile reset will clear:

- 01-renter-profile.md — [has content / already a placeholder template]
  Full file will be replaced with a blank template.

- 02-tenant-profile.md — [has content / already a placeholder template]
  Full file will be replaced with a blank template.

- 04-flat-evaluation.md — [commute filled in / already a placeholder]
  [YOUR_MAX_COMMUTE] will be reset to a placeholder. Scoring framework preserved.

- search-criteria.md — [commute filled in / already a placeholder]
  [YOUR_MAX_COMMUTE] will be reset to a placeholder. Portal list and queries preserved.

- CLAUDE.md — Renter Profile section will be reset to placeholders.

The following files are NOT touched (they contain framework rules, not personal data):
  - 03-writing-style.md
  - 05-selbstauskunft-templates.md
  - 06-anschreiben-templates.md
  - 07-besichtigung-prep.md
```

### If scope includes `documents`:

Use Glob to list all files present in `documents/income/`, `documents/employer/`, `documents/credit/`, `documents/landlord_refs/`, and `documents/inquiries/`. Present as:

```
## Documents reset will delete:

documents/income/
  - [filename] or "(empty)"

documents/employer/
  - [filename] or "(empty)"

documents/credit/
  - [filename] or "(empty)"

documents/landlord_refs/
  - [filename] or "(empty)"

documents/inquiries/
  - [subfolder/filename] or "(empty)"

documents/README.md — NOT deleted (instructions file)
```

If all document subfolders are already empty, state "All document subfolders are already empty — nothing to delete." and skip the confirmation step for this scope.

---

## Step 2: Require Explicit Confirmation

Present the confirmation prompt:

> **This cannot be undone.**
>
> Type **`RESET`** (all caps) to confirm, or anything else to cancel.

Wait for the user's response.

- If the user types exactly `RESET`: proceed to Step 3.
- If the user types anything else: abort and tell them "Reset cancelled. Nothing was changed."

---

## Step 3: Execute the Reset

### Profile reset

**For `01-renter-profile.md`**, replace the file content with:

```markdown
# Renter Profile

<!-- Run /setup to populate this file -->

## Identity

## Household

## Employment & Income

## Creditworthiness & Rental History

## Search Profile

## References

## Past Inquiries
```

**For `02-tenant-profile.md`**, replace the file content with:

```markdown
# Tenant Profile

<!-- Run /setup to populate this file -->

## Overview

## Core Traits Landlords Screen For

## Strongest Selling Points

## How You Come Across

## Things to Address Proactively

## Mapping to Listing Language

## Landlord/Agent Interaction Preferences

## Using This in Anschreiben
```

**For `04-flat-evaluation.md`**, locate every occurrence of a filled-in commute value in the "Commute Fit" sections and replace with `[YOUR_MAX_COMMUTE]`. Leave the rest of the file (scoring tables, weighting, thresholds) intact.

**For `search-criteria.md`**, locate the filled-in commute value under "Max commute" and replace with `[YOUR_MAX_COMMUTE]`. Leave portal lists and queries intact.

**For `CLAUDE.md`**, replace the content of the "## Renter Profile" section (down to, but not including, "## Repo Structure") with the placeholder version shown in the framework's original template (all `[PLACEHOLDER]` tokens).

### Documents reset

For each non-empty document subfolder, delete all files within it using Bash `rm`. Do not delete the folder itself, and do not delete `documents/README.md`.

```bash
rm -f documents/income/*
rm -f documents/employer/*
rm -f documents/credit/*
rm -f documents/landlord_refs/*
rm -rf documents/inquiries/*/
```

---

## Step 4: Confirm What Was Done and Next Steps

After the reset is complete, report:

```
## Reset complete

### Cleared
[List each file/folder that was actually modified or cleared]

### Unchanged
[List anything that was already empty or was intentionally preserved]
```

Then tell the user what to do next based on what was reset:

**If profile was reset:**
> Your renter profile is now blank. Run `/setup` to repopulate it. The command auto-detects any files in your `documents/` folder and offers to read from there; otherwise it walks you through pasting your details or an interactive interview.

**If documents were reset:**
> The `documents/` folder is now empty. Add your income proof, employer letter, Schufa, and landlord references, and run `/setup` to populate your profile. See `documents/README.md` for instructions on what to put where.

**If both were reset:**
> Both your profile files and documents folder are now empty. Add documents to `documents/` (or skip and paste your details / use the interview), then run `/setup`.
