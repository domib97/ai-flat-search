# Selbstauskunft (Self-Disclosure Form) Template and Tailoring Guide

## Template: Plain LaTeX article (lualatex)

The Mieterselbstauskunft is a structured, mostly-static one-pager. Unlike a CV, it does not need heavy tailoring per listing - the same facts apply everywhere. Only the "Angaben zum gewünschten Mietverhältnis" section changes per listing (desired move-in date, household size if it varies by flat size, sometimes the offered rent).

**Output file:** `selbstauskunft/selbstauskunft_<address-slug>.tex` (a fresh copy per listing only if something listing-specific must change; otherwise reuse `selbstauskunft_example.tex` as the canonical file and just update the move-in date)
**Compile with:** **lualatex**
**Master reference:** `selbstauskunft/selbstauskunft_example.tex`

### Compile command

```bash
cd selbstauskunft && lualatex -interaction=nonstopmode selbstauskunft_<address-slug>.tex
```

Expected output: exactly 1 page. Any other page count is a failure that must be fixed before presenting to the user.

## Document Structure

```latex
\documentclass[11pt,a4paper]{article}
\usepackage[utf8]{inputenc}
\usepackage[ngerman]{babel}
\usepackage[margin=2.2cm]{geometry}
\usepackage{parskip}
\usepackage{array}
\usepackage{enumitem}
\usepackage{xcolor}
\definecolor{accent}{HTML}{2F5C8A}

\newcommand{\formsection}[1]{{\large\bfseries\color{accent}#1}\par\vspace{2pt}\hrule\vspace{6pt}}
\newcommand{\field}[2]{\textbf{#1:} #2\\[2pt]}

\pagestyle{empty}

\begin{document}

\begin{center}
{\Large\bfseries Mieterselbstauskunft}\\[2pt]
[YOUR_NAME]
\end{center}
\vspace{8pt}

\formsection{1. Persönliche Daten}
\field{Name, Vorname}{[LAST_NAME], [FIRST_NAME]}
\field{Geburtsdatum}{[YOUR_DOB]}
\field{Aktuelle Anschrift}{[YOUR_CURRENT_ADDRESS]}
\field{Telefon}{[YOUR_PHONE]}
\field{E-Mail}{[YOUR_EMAIL]}

\formsection{2. Beruf und Einkommen}
\field{Beruf}{[JOB_TITLE]}
\field{Arbeitgeber}{[NEW_EMPLOYER], Walldorf}
\field{Beschäftigungsverhältnis}{[unbefristet/befristet seit/ab DATUM]}
\field{Nettoeinkommen (monatlich)}{[YOUR_NET_INCOME]}

\formsection{3. Haushalt}
\field{Anzahl einziehender Personen}{[N]}
\field{Haustiere}{[NONE_OR_DESCRIBE]}
\field{Raucher}{[Nein/Ja]}

\formsection{4. Bonität}
\field{Schufa-Bonitätsauskunft}{[liegt vor / wird nachgereicht]}
\field{Mietschuldenfreiheitsbescheinigung}{[liegt vor / wird nachgereicht]}
\field{Bisherige Mietdauer am aktuellen Wohnsitz}{[YEARS]}
\field{Mietrückstände}{Keine}

\formsection{5. Angaben zum gewünschten Mietverhältnis}
\field{Gewünschter Einzugstermin}{[DESIRED_MOVE_IN_DATE]}
\field{Grund des Umzugs}{Neue Arbeitsstelle bei [NEW_EMPLOYER] in Walldorf ab [START_DATE]}

\vspace{14pt}
\formsection{Erklärung}
Ich versichere, dass die obigen Angaben der Wahrheit entsprechen.
\vspace{24pt}

\noindent
\makebox[6cm]{\hrulefill} \hfill \makebox[6cm]{\hrulefill}\\
\makebox[6cm][l]{Ort, Datum} \hfill \makebox[6cm][l]{Unterschrift}

\end{document}
```

## Tailoring Guidelines

### What changes per listing
- **Section 5 only**, in most cases: desired move-in date (if the listing has a fixed availability date, align to it rather than the generic profile default), and household size if a specific flat size makes one configuration of the household more relevant to mention.
- Everything else (Sections 1-4) is static and pulled directly from `01-renter-profile.md`. Do not re-derive or rephrase these facts per listing.

### What never changes
- Income, employment, and Bonität facts must be **identical** across every Selbstauskunft sent out. Inconsistent figures across applications is the fastest way to lose credibility if a landlord compares notes with another landlord (rare, but it happens in smaller towns) or simply notices a typo-level discrepancy upon a second look.

### Optional additions (only if the user has the supporting document)
- A short "Referenzen" line citing the previous landlord, if `01-renter-profile.md` lists one and the listing seems to value it (e.g. an explicit ad request for references)
- A Guarantor (Bürge) section, only if `01-renter-profile.md` records one - never invent a guarantor

## Compile-and-Inspect Loop (MANDATORY)

After writing the Selbstauskunft and before presenting to the user, always compile and visually inspect the PDF:

1. Run `lualatex -interaction=nonstopmode selbstauskunft_<address-slug>.tex`
2. Check the output page count: must be exactly 1
3. Read the PDF via the Read tool and visually inspect: all fields populated (no leftover `[PLACEHOLDER]` tokens), no overflow, signature line fits

### Fixing overflow
If the form overflows to a second page (rare, since content is fixed), reduce `\vspace` values inside `\formsection` and between sections proportionally - do not cut content from a legal disclosure form.
