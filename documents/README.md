# Documents Folder

This folder holds your actual renter documents. The `/setup` command reads everything here and uses it to populate the renter profile files under `.claude/skills/flat-application-assistant/`. It is safe to re-run `/setup` as you add new documents — it merges intelligently and will never overwrite existing content without asking you first.

---

## Folder Structure

```
documents/
├── income/                      # Payslips, proof of income
├── employer/                    # New job contract / offer letter (Sankt Leon-Rot employer)
├── credit/                      # Schufa-Bonitätsauskunft
├── landlord_refs/               # Previous landlord references / Mietschuldenfreiheitsbescheinigung
├── inquiries/                   # Past flat inquiries
│   └── <address-slug>/
│       ├── listing.md           # The original listing text (paste as text)
│       ├── anschreiben.tex      # The Anschreiben you sent
│       └── outcome.md           # Result + notes (fill in after hearing back)
└── README.md                    # This file
```

---

## income/

Proof of income for the new job (or current job, if income is unchanged): payslips (Gehaltsabrechnungen), or an employer letter stating salary.

**Supported formats:** `.pdf`, `.txt`, `.md`

**What `/setup` extracts:**
- Net monthly income
- Employer name (cross-referenced against `employer/`)
- Pay period / contract type

**Naming:** Any filename works. If multiple files are present, `/setup` reads all of them and cross-references for consistency.

---

## employer/

The new job's contract or offer letter — the document that actually explains the move.

**Supported formats:** `.pdf`, `.txt`, `.md`

**What `/setup` extracts:**
- Job title
- Employer name and Sankt Leon-Rot as the work location
- Contract type (unbefristet/befristet, Probezeit)
- Start date

**Naming:** Any filename works.

---

## credit/

Your Schufa-Bonitätsauskunft, if you've already obtained one.

**Supported formats:** `.pdf`

**What `/setup` extracts:**
- Date the Schufa-Auskunft was issued
- Any summary score visible

**Naming:** Any filename works.

---

## landlord_refs/

References from a previous or current landlord: a reference letter, or a Mietschuldenfreiheitsbescheinigung (confirmation of no outstanding rent).

**Supported formats:** `.pdf`, `.txt`, `.md`

**What `/setup` extracts:**
- Previous landlord name and contact (optional, for the references section)
- Tenancy dates
- Confirmation of no arrears

**Naming:** Use a descriptive name, e.g. `mietschuldenfreiheit_mustermann.pdf`.

---

## inquiries/

A record of past flat inquiries. Each subfolder is one listing you wrote to.

**Subfolder naming:** `<address-slug>` — lowercase, underscores for spaces, e.g. `musterstrasse_12_karlsruhe`.

Examples:
```
inquiries/
├── musterstrasse_12_karlsruhe/
├── hauptstrasse_5_heidelberg/
└── bahnhofstrasse_3_bruchsal/
```

### Files within each inquiry folder

**`listing.md`** — Paste the full listing text here. Used by `/setup` to refine `04-flat-evaluation.md` over time (which listings actually fit, which didn't).

**`anschreiben.tex`** — The Anschreiben you actually sent. Used to check writing-style consistency.

**`outcome.md`** — Fill this in after the inquiry resolves. Format:

```markdown
# Outcome: <Address>

**Status:** viewing_offered | rejected | no_response | signed

**Date resolved:** YYYY-MM-DD

## Notes
What happened? Did the landlord give feedback (e.g. "too many applicants", "went with someone local")?
Was the listing still actually available when you wrote in, or already gone?
```

**What `/setup` learns from outcome.md:**
- Whether income-strong applications are still losing out (signals competition volume rather than profile weakness)
- Whether listings are commonly already gone by the time you write in (signals the search needs to run more frequently or target fresher listings)

---

## File Format Notes

| Format | Readable by `/setup` | Notes |
|--------|--------------------------|-------|
| `.pdf` | Yes | Parsed directly with the Read tool |
| `.md` / `.txt` | Yes | Plain text |
| `.docx` | No | Convert to PDF before placing here |
| `.png` / `.jpg` | No | Scanned documents won't be parsed — use text PDFs |

---

## Re-running `/setup`

The command is designed to be re-run as your document collection grows. Each run:

1. Reads the current state of `01-renter-profile.md` and `02-tenant-profile.md`
2. Compares extracted document content against what's already there
3. Only proposes changes for content that is genuinely new or conflicting
4. Never silently overwrites — conflicts are shown explicitly for your decision

**When to re-run:**
- After obtaining your Schufa-Bonitätsauskunft
- After receiving your signed employment contract
- After recording outcomes for inquiries you've sent
- After your income or household situation changes

---

## A note on sensitive documents

Everything in this folder (besides this README and the folder structure) is personal financial and identity data and is excluded from git via `.gitignore`. Do not commit it, and be mindful about which Anschreiben/Selbstauskunft drafts you send where — never send the original Schufa/payslip files to anyone other than a landlord you're actually applying to.
