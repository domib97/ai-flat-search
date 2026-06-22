# Anschreiben Templates and Tailoring Guide

## Template: Custom cover.cls (XeLaTeX)

Inquiry letters (Anschreiben) use the existing custom LaTeX document class (`cover.cls`) with Lato/Raleway fonts, unchanged from its original purpose.

**Output file:** `anschreiben/anschreiben_<address-slug>.tex`
**Compile with:** XeLaTeX (cover.cls requires fontspec)
**Font directory:** `anschreiben/OpenFonts/fonts/`

### Compile command

```bash
cd anschreiben && xelatex -interaction=nonstopmode anschreiben_<address-slug>.tex
```

Expected output: `Output written on anschreiben_<address-slug>.pdf (1 page, ...)`. Any page count other than 1 is a failure that must be fixed before presenting to the user.

## Compile-and-Inspect Loop (MANDATORY)

After writing the Anschreiben and before presenting to the user, always compile and visually inspect the PDF. Iterate until the layout is clean:

1. Run `xelatex -interaction=nonstopmode anschreiben_<address-slug>.tex`
2. Confirm page count is exactly 1 and compile succeeded
3. Read the PDF via the Read tool and visually check: signature fits at the bottom, no text cut off, bullet font matches body

### Known template pitfall: itemize inside `\lettercontent{}`

The `\lettercontent{}` macro appends `\\` to its argument. This breaks when the argument ends in `\end{itemize}` because `\\` has no line to break after the environment closes, producing `! LaTeX Error: There's no line here to end.` and no PDF output.

**Wrong (breaks compile):**
```latex
\lettercontent{Das passt aus folgenden Gründen:
\begin{itemize}
    \item ...
\end{itemize}}
```

**Correct - close `\lettercontent{}` before the list and wrap the list in the matching Raleway-Medium font so typography stays consistent:**
```latex
\lettercontent{Das passt aus folgenden Gründen:}

{\raggedright\fontspec[Path = OpenFonts/fonts/raleway/]{Raleway-Medium}\fontsize{11pt}{13pt}\selectfont
\begin{itemize}
    \item ...
\end{itemize}\par}
\vspace{6pt}

\lettercontent{[nächster Absatz]}
```

The font wrapper is mandatory - if you just move `\begin{itemize}` outside `\lettercontent{}` without the `\fontspec` block, bullets render in the default body font (Lato) and visually mismatch the rest of the letter.

## Document Structure

```latex
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Anschreiben - [Address], [Portal]
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

\documentclass[]{cover}
\usepackage{fancyhdr}

\pagestyle{fancy}
\fancyhf{}

\rfoot{Seite \thepage \hspace{0pt}}
\thispagestyle{empty}
\renewcommand{\headrulewidth}{0pt}
\begin{document}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%     TITLE NAME
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\namesection{}{\Huge{[YOUR_NAME]}}{  \href{mailto:[YOUR_EMAIL]}{[YOUR_EMAIL]} | [YOUR_PHONE]
}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%     MAIN LETTER CONTENT
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

\currentdate{\today}
\lettercontent{Sehr geehrte Damen und Herren,}

\lettercontent{mit großem Interesse habe ich Ihr Inserat für [LISTING_SHORT_DESCRIPTION] auf [PORTAL] gesehen. [ONE_SPECIFIC_DETAIL_FROM_THE_LISTING - layout/Ausstattung/Lage].}

\lettercontent{Wir, [HOUSEHOLD_DESCRIPTION], ziehen aufgrund einer neuen Arbeitsstelle bei [NEW_EMPLOYER] in Walldorf um, die [START_DATE] beginnt. Gerne würden wir zum [DESIRED_MOVE_IN_DATE] einziehen.

\begin{itemize}
    \item \textbf{Einkommen:} Nettoeinkommen von [YOUR_NET_INCOME], unbefristetes Arbeitsverhältnis
    \item \textbf{Bonität:} [Schufa liegt vor / wird gerne nachgereicht], keine Mietrückstände
    \item \textbf{Haushalt:} [N] Person(en), [Nichtraucher/Raucher], [keine Haustiere / Haustier-Details]
    \item \textbf{Verfügbarkeit:} Kurzfristig für einen Besichtigungstermin verfügbar
\end{itemize}

Unsere ausgefüllte Selbstauskunft sowie [Einkommensnachweise/Schufa - nur was tatsächlich existiert] fügen wir bei bzw. reichen gerne nach.}

\lettercontent{Wir freuen uns sehr über eine Rückmeldung und stehen für Fragen oder einen Besichtigungstermin jederzeit zur Verfügung.}

\begin{flushright}
\closing{Mit freundlichen Grüßen,\\}

\signature{[YOUR_NAME]}
\end{flushright}
\end{document}
```

## Key Commands Reference

| Command | Purpose |
|---------|---------|
| `\namesection{}{Name}{contact info}` | Header with name and contact |
| `\currentdate{date}` | Date field (use `\today` or explicit date) |
| `\lettercontent{text}` | Body paragraph (adds spacing after) |
| `\closing{text}` | Closing line |
| `\signature{name}` | Printed name below signature |

## Tailoring Guidelines

### Salutation
- If a named contact is given in the listing: "Sehr geehrte/r Herr/Frau [Name],"
- Generic: "Sehr geehrte Damen und Herren," (default for most Kleinanzeigen/WG-Gesucht listings, which rarely name a contact)

### Length - Hard 1-Page Limit
- Target: 120-180 words of body text (shorter than a job cover letter - see `03-writing-style.md`)
- Maximum: **never exceed 1 page**
- Always include: opening with listing reference, household/move reason paragraph + bullet list, closing. That's the whole letter - don't pad it.

### Bullet Lists
- Place `\begin{itemize}...\end{itemize}` **outside** a `\lettercontent{}` block (see "Known template pitfall" above), wrapped in the matching Raleway-Medium `\fontspec` so the bullet font matches the body
- 3-5 bullets is ideal
- Use `\textbf{Label:}` for category-style bullets (Einkommen, Bonität, Haushalt, Verfügbarkeit)

### LaTeX Special Characters
- Underscore: `\_`
- Ampersand: `\&`
- German umlauts and ß render natively with `babel[ngerman]`/XeLaTeX - no escaping needed

### English Anschreiben (international listings)
- Same template structure, write content in English, adjust closing to "Kind regards,"
- Only use this when the listing itself is in English (see `03-writing-style.md`)

## Checklist Before Finalizing
- [ ] No em-dashes (use commas or periods instead)
- [ ] No rental clichés (see `03-writing-style.md` banned phrases)
- [ ] References a specific detail from this listing, not generic
- [ ] Every fact (income, contract type, household) matches `01-renter-profile.md` exactly
- [ ] No document is claimed as attached unless it actually exists per the profile
- [ ] Address/portal reference is correct throughout
- [ ] Date is current
- [ ] Fits on one page
- [ ] Language matches the listing's language (German by default)
- [ ] Salutation is appropriate (named person if known, otherwise "Sehr geehrte Damen und Herren,")

## Sending Guidelines (Best Practice)
- This framework drafts the PDF only - it never sends the message or logs into a portal.
- Export as PDF before sending; attach the Selbstauskunft PDF alongside it if the user has one ready.
- Name files clearly: "[Your Name] Anschreiben [Address]" and "[Your Name] Selbstauskunft".
- Follow whatever submission channel the listing specifies (portal message, email, WhatsApp) - the user sends it themselves.
