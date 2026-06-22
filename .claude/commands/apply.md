# /apply - Drafter-Reviewer Flat Inquiry Workflow

You are orchestrating a two-agent flat inquiry workflow. The listing is provided below as `$ARGUMENTS` (either a URL or pasted text).

Follow these steps **exactly in order**. Do not skip steps.

**This workflow never sends anything.** It drafts a Selbstauskunft and Anschreiben to disk for the user's review. It does not submit a portal inquiry form, send an email, or message a landlord.

**Token-efficiency rules for this workflow:**
- Never re-Read a file whose contents are already in your context from an earlier step.
- When dispatching the reviewer agent, pass draft content **inline in the agent prompt** rather than asking the agent to Read files you already have in memory.
- Run the full verification checklist exactly once, at the end (Step 6).
- Step 5 (compile and inspect PDFs) is mandatory and non-skippable.

---

## Step 0: Parse Input

- If `$ARGUMENTS` looks like a URL, use `WebFetch` to retrieve the listing content. If the portal blocks the fetch (common for ImmoScout24, occasional on others), ask the user to paste the listing text instead - do not retry the same blocked domain repeatedly.
- If it is pasted text, use it directly.
- Extract: **address/area**, **portal**, **Warmmiete or Kaltmiete + Nebenkosten**, **rooms**, **size (m²)**, **availability date**, **required documents**, **contact channel** (portal message, email, phone).
- Store these for use throughout the workflow.

---

## Step 1: DRAFTER - Evaluate Fit

Read the evaluation framework:
- `.claude/skills/flat-application-assistant/04-flat-evaluation.md`
- `.claude/skills/flat-application-assistant/01-renter-profile.md`

Estimate the commute from the listing's address to Walldorf via WebSearch/WebFetch (Google Maps or VRN/KVV journey planner) - do not guess a number.

Using the framework from `04-flat-evaluation.md`, evaluate the listing. Present the evaluation to the user with:

1. **Price fit** - Warmmiete vs. budget (flag if estimated from Kaltmiete)
2. **Commute fit** - estimated time to Walldorf, by what mode
3. **Location & area fit**
4. **Size/layout/must-haves** - pass/fail against deal-breakers
5. **Application competitiveness** - income multiple, posting freshness
6. **Overall fit score** and recommendation

**Before presenting the evaluation, check for scam red flags** (`07-besichtigung-prep.md`). If any are present, stop here and tell the user directly instead of proceeding with an evaluation.

After presenting the evaluation, ask the user:
> "Should I draft the Selbstauskunft and Anschreiben for this listing?"

**If the user says no, stop here.** If yes, continue to Step 2.

---

## Step 2: DRAFTER - Update Selbstauskunft + Draft Anschreiben

You already have `01-renter-profile.md` and `04-flat-evaluation.md` in context from Step 1. **Do not re-read them.**

Read only the reference files you do not yet have:
- `.claude/skills/flat-application-assistant/03-writing-style.md`
- `.claude/skills/flat-application-assistant/05-selbstauskunft-templates.md`
- `.claude/skills/flat-application-assistant/06-anschreiben-templates.md`
- `.claude/skills/flat-application-assistant/02-tenant-profile.md`

Also read the existing Selbstauskunft for reference:
- `selbstauskunft/selbstauskunft_example.tex` (or the most recent listing-specific copy)

### Selbstauskunft (`selbstauskunft/selbstauskunft_<address-slug>.tex`)
- Follow `05-selbstauskunft-templates.md`
- Only Section 5 (move-in date, household framing) should change from the reference copy - everything else must be identical to `01-renter-profile.md`
- If nothing listing-specific needs to change, tell the user the existing Selbstauskunft already applies rather than creating a near-duplicate file

### Anschreiben (`anschreiben/anschreiben_<address-slug>.tex`)
- Follow the writing style rules in `03-writing-style.md` (critical: no em-dashes, no rental clichés, formal "Sie")
- Follow the template structure in `06-anschreiben-templates.md`
- Must reference at least one specific detail from this listing
- Connect the household's situation (new job in Walldorf) to this specific listing
- Only mention documents as "attached" if they actually exist per `01-renter-profile.md`

Write both files to disk. Keep the exact text in working memory for Steps 3-4.

---

## Step 3: REVIEWER - Verify & Critique

Use the **Agent tool** to spawn a `general-purpose` reviewer agent. The reviewer gets a fresh context, so pass the drafts **inline in the prompt** below.

Replace `<ADDRESS>`, `<PORTAL>`, `<INSERT_LISTING_TEXT_HERE>`, `<INSERT_SELBSTAUSKUNFT_DRAFT_HERE>`, and `<INSERT_ANSCHREIBEN_DRAFT_HERE>` with actual values before dispatching.

```
You are reviewing a flat rental inquiry before it is sent to a landlord. Your job is to catch errors, generic phrasing, and anything that could read as a scam-adjacent or untrustworthy message - and to make the inquiry as targeted as possible.

## Your Tasks

### 1. Verify the Listing-Specific Claims
Re-check the listing text below against the Anschreiben draft: does every specific detail mentioned (layout, fittings, location feature) actually appear in the listing? Flag any invented detail.

### 2. Check for Scam Red Flags
Use WebSearch if useful (e.g. to check whether the address/price combination looks plausible for the area). Flag if anything about the listing itself looks like a known rental scam pattern (price far below market, no real address, landlord claiming to be unavailable for viewings).

### 3. Read Reference Materials (content-critique only)
Read these three files - and only these - to ground your critique:
- `.claude/skills/flat-application-assistant/01-renter-profile.md`
- `.claude/skills/flat-application-assistant/02-tenant-profile.md`
- `.claude/skills/flat-application-assistant/03-writing-style.md`

Do NOT read `05-selbstauskunft-templates.md` or `06-anschreiben-templates.md` - those govern LaTeX structure the drafter already applied.

### 4. Drafts to Review
Both drafts are provided inline below. Do NOT use the Read tool on the draft files - use these exact texts.

<SELBSTAUSKUNFT_DRAFT file="selbstauskunft/selbstauskunft_<ADDRESS>.tex">
<INSERT_SELBSTAUSKUNFT_DRAFT_HERE>
</SELBSTAUSKUNFT_DRAFT>

<ANSCHREIBEN_DRAFT file="anschreiben/anschreiben_<ADDRESS>.tex">
<INSERT_ANSCHREIBEN_DRAFT_HERE>
</ANSCHREIBEN_DRAFT>

### 5. Listing
<LISTING portal="<PORTAL>">
<INSERT_LISTING_TEXT_HERE>
</LISTING>

### 6. Produce Feedback

Return your feedback in **two parts**:

**Part A - Structured edits (preferred format whenever possible):**
A JSON array of concrete edits the drafter can apply directly:
```json
{
  "file": "selbstauskunft/selbstauskunft_<ADDRESS>.tex" | "anschreiben/anschreiben_<ADDRESS>.tex",
  "old_string": "<exact text currently in the draft>",
  "new_string": "<replacement text>",
  "reason": "<one-line rationale>"
}
```
Only use this format when you can quote the exact `old_string` from the drafts above, with enough surrounding context to match exactly once per file.

**Part B - Narrative suggestions:**
Prose suggestions grouped by category. Produce each category even if your finding is "no issues":
- **Fabricated or unverifiable listing details** - anything in the Anschreiben not actually supported by the listing text
- **Scam risk assessment** - any concern about the listing itself, separate from the drafts
- **Generic phrasing** - rental clichés or copy-paste-sounding lines that don't read as specific to this listing
- **Tone and consistency issues** - check against `03-writing-style.md` and `02-tenant-profile.md`; flag any inconsistency between the Selbstauskunft and Anschreiben

**CRITICAL RULE:** Do NOT suggest fabricating income, employment, household, or document-availability claims. If something is a genuine gap (e.g. no Schufa yet), say so honestly and suggest how to phrase it ("wird gerne nachgereicht") rather than implying it exists.

Return Part A and Part B together as a single structured message.
```

---

## Step 4: DRAFTER - Revise Based on Feedback

Once the reviewer agent returns its feedback:

1. **Apply Part A (structured edits) directly with the Edit tool.** Do NOT re-read the draft files. Skip any whose rationale would require fabricating content.
2. **Apply Part B (narrative suggestions)** using judgment:
   - **Fabricated/unverifiable details:** remove or rephrase to only what the listing text actually supports.
   - **Scam risk assessment:** if the reviewer raises a real concern, stop and surface it to the user before continuing - do not silently proceed.
   - **Generic phrasing:** rewrite to reference the specific listing detail more concretely.
   - **Tone/consistency issues:** apply the writing-style-guide fixes.
3. Do NOT incorporate any suggestion that would fabricate income, employment, household, or document claims.

After all edits are applied, the two files on disk are the final drafts.

---

## Step 5: DRAFTER - Compile & Inspect PDFs (MANDATORY)

**Never skip this step.**

### 5a. Compile

```bash
cd selbstauskunft && lualatex -interaction=nonstopmode selbstauskunft_<address-slug>.tex
cd ../anschreiben && xelatex -interaction=nonstopmode anschreiben_<address-slug>.tex
```

- Selbstauskunft uses **lualatex**.
- Anschreiben uses **xelatex** (cover.cls requires fontspec).

If either compile fails, fix the error and re-compile until clean.

### 5b. Inspect layout

Read both PDFs via the Read tool and verify:

**Selbstauskunft:**
- [ ] Exactly 1 page
- [ ] No leftover `[PLACEHOLDER]` tokens
- [ ] Signature line fits at the bottom

**Anschreiben:**
- [ ] Exactly 1 page
- [ ] Signature block visible, not cut off
- [ ] Bullet list font matches surrounding body text (both should be Raleway-Medium)

### 5c. Iterate until clean

See `05-selbstauskunft-templates.md` and `06-anschreiben-templates.md` for common fixes (itemize/fontspec wrapping, vspace adjustments).

### 5d. Clean up build artifacts

After the final clean compile, delete the `.aux`, `.log`, `.out` files (keep the `.tex` and `.pdf`).

---

## Step 6: Present Final Output

Run the full verification checklist from `CLAUDE.md` now - this is the **only** verification pass in the workflow.

### Verification Checklist
Report pass/fail for each item in the CLAUDE.md verification checklist.

### Key Tailoring Decisions
Summarize 2-4 key decisions made to tailor the inquiry:
- Which listing detail was referenced and why
- What the reviewer flagged that was most impactful
- Any scam-risk assessment, even if "no concerns found"

### Files Created
List the files written:
- `selbstauskunft/selbstauskunft_<address-slug>.tex` (or note that the existing one was reused unchanged)
- `anschreiben/anschreiben_<address-slug>.tex`

Tell the user: "Both files are ready for your review. **You send them yourself** - this workflow never submits an inquiry or contacts the landlord. Open the PDFs to check the final output, then send via whatever channel the listing specifies (portal message, email, or phone first if it's a fast-moving listing - see `07-besichtigung-prep.md`)."
