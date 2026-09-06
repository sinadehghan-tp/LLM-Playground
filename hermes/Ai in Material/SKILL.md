---
name: paper-finder
description: Find and deduplicate two complementary recent polymer and materials-AI journal papers.
version: 0.1.0
author: sinad, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [research, conjugated-polymers, OMIEC, materials-ai, bayesian-optimization, self-driving-labs, literature]
    related_skills: []
---

# Daily Paper Finder

Each day, find and deliver two distinct peer-reviewed journal papers (published 2024-01-01 or later, prioritizing 2025+): one focused on experimental conjugated-polymer formulation, solution conformation, aggregation, or processing even when it has little or no AI; and one focused on genuine materials AI/ML, active learning, Bayesian optimization, autonomous experimentation, or high-throughput discovery. Verify every detail against a real source, never fabricate, and never send the same paper twice.

## When to Use

- Run the daily scheduled paper-scout cycle.
- Use when the user asks for a fresh relevant paper or to review a candidate.
- Use when adjusting the recurring Telegram paper scout.
- Do not use for job searches (that is the separate `job-finder` skill and `job_finder` directory).

## Working Directory and State

- The scheduled job pins its workdir to `D:\PhD_Code\LLM-Playground\hermes\Ai in Material`, keeping code, instructions, and deduplication state isolated from other Telegram projects.
- Maintain `paper-sent.json` in this directory: a JSON array of every previously sent paper.
- Telegram destination: the "Ai in Material" channel, chat ID `-1003543452515`.

## Two Required Research Tracks

Select one distinct paper from each track. Do not rank the two tracks against each other and do not use one paper to fill both slots.

### Track 1 — Conjugated-polymer formulation and conformation

AI is not required and must not be used to penalize a strong experimental polymer paper. Rank candidates in this order:

1. Conjugated-polymer formulation, solution conformation, aggregation, self-assembly, solubility, polymer–solvent interactions, and processing–structure relationships.
2. OMIEC formulation, morphology, conformation, aggregation, and processing.
3. OPV, OECT, OFET, or OPD studies that experimentally connect formulation, processing, solution structure, aggregation, or morphology to device performance.
4. Experimental characterization of the above using methods such as DLS/SLS, SANS/SAXS/GIWAXS, UV–Vis, spectroscopy, scattering, microscopy, or rheology.

### Track 2 — Materials AI and autonomous experimentation

AI/ML or data-driven experimental optimization must be a core method or contribution, not a superficial mention. Rank candidates in this order:

1. AI/ML for conjugated polymers, organic electronic materials, polymer formulation, morphology, processing, or device optimization, especially with experimental validation.
2. Self-driving laboratories, autonomous experimentation, robotic labs, and closed-loop materials discovery, especially for polymers or organic materials.
3. Bayesian optimization and active learning for experimental design or materials optimization.
4. AI/scientific/LLM agents combined with Bayesian optimization or laboratory automation.
5. High-throughput experimental screening with ML for materials discovery.
6. Gaussian processes, graph neural networks, uncertainty quantification, out-of-distribution generalization, kernel methods, or interpretable models when the materials relevance is substantive.

## Exclusions

Apply before selecting:

1. Exclude papers published before 2024-01-01.
2. Exclude papers primarily based on molecular dynamics, DFT, Monte Carlo, finite-element modeling, CFD, or other simulation-only research.
3. Exclude papers where materials science is only a superficial application.
4. Exclude inorganic-only materials papers unless the methodology is exceptionally transferable to conjugated polymers, polymer formulations, OMIECs, organic electronic devices, or autonomous labs.
5. Exclude editorials, news articles, patents, theses, conference abstracts, and unreviewed preprints when a peer-reviewed journal version exists.
6. Never send the same paper twice (dedupe on DOI, then normalized title).
7. Never invent titles, findings, publication details, or links.

## Discovery Sources

Search reliable scholarly databases and official journal/publisher websites. Use `web_search` and `web_extract`, and `terminal`+`curl` for structured APIs. Suggested sources:

- Crossref API: `https://api.crossref.org/works?query=...&filter=from-pub-date:2024-01-01,type:journal-article&sort=published&order=desc&rows=20`
- Publisher sites: ACS, RSC, Wiley, Nature Portfolio, Science/AAAS, Elsevier/ScienceDirect, IOP, APS.
- Google Scholar / Semantic Scholar (`https://api.semanticscholar.org/graph/v1/paper/search`) for discovery only; always verify on the publisher page.
- OpenAlex (`https://api.openalex.org/works?filter=...`) for discovery and metadata cross-checks.

Rotate targeted query families across the priorities, e.g.:

- `machine learning conjugated polymer`, `AI organic electronic materials`, `machine learning polymer formulation morphology`, `data-driven organic electronics`
- `self-driving laboratory polymers`, `autonomous experimentation organic materials`, `closed-loop materials discovery`
- `Bayesian optimization polymer formulation`, `active learning experimental materials design`
- `conjugated polymer aggregation formulation`, `conjugated polymer solution conformation`, `processing structure conjugated polymer`
- `OMIEC morphology`, `mixed ionic electronic conductor formulation`, `data-driven OMIEC`
- `organic electrochemical transistor morphology`, `organic photovoltaic formulation morphology`, `OFET film morphology processing`, `organic photodetector`
- `LLM agent Bayesian optimization laboratory`, `high throughput screening machine learning materials`
- `Gaussian process materials`, `graph neural network polymer`, `uncertainty quantification materials`

## Search Bounds

- At most ~14 `web_search` and ~18 `web_extract` calls per cycle; API calls may supplement.
- Batch independent searches.
- Never loop retries on an empty or failed source.
- If a paper cannot be verified, exclude it rather than guessing.

## Selection Procedure

1. Read `paper-sent.json` and build the set of known DOIs and normalized titles.
2. Search the sources above for candidates published 2024-01-01 or later (strongly prefer 2025+).
3. For each promising candidate, verify existence and correctness of title, journal, publication year, DOI, and link — using the abstract and paper information, not the title alone.
4. Prefer the DOI (`https://doi.org/<doi>`) or official publisher page as the primary link.
5. Apply exclusions and rank candidates independently within the two required tracks.
6. Select the single best unsent Track 1 paper and the single best unsent Track 2 paper. They must be two distinct papers. For Track 1, prefer direct experimental formulation/conformation relevance regardless of AI. For Track 2, prefer AI connected to experiments, then conjugated-polymer/OMIEC/organic-electronics relevance, then recency.
7. Append both selected papers to `paper-sent.json` atomically before/at delivery; keep the file valid JSON. If only one track yields a suitable verified unsent paper, append and send only that paper plus the exact missing-track sentence below; never lower standards or fabricate a second paper.

## Deduplication Registry

`paper-sent.json` is a JSON array; each entry has:

- `doi`
- `title`
- `journal`
- `year`
- `url`
- `date_sent`
- `category`
- `track` (`polymer-formulation-conformation` or `materials-ai`)

Rules:

1. Identity is `doi` when available; otherwise normalized title (lowercased, punctuation/whitespace collapsed).
2. Preserve existing entries; append the new one and write atomically.
3. Never select a paper whose DOI or normalized title already appears.
4. URL tracking parameters or formatting differences do not create a new paper.

## Telegram Output Format

Deliver exactly one message containing two labeled paper blocks, under 400 words total, in plain scientific language with no exaggerated claims. Format:

```text
🧪 **Polymer formulation / conformation pick**

📄 **Paper title**

**Journal and year:** Journal, Year
**Category:** [one or two categories from the priorities]

**Short summary:**
Concise sentences on the research question, methodology, and main finding.

**Why it matters to my research:**
One sentence connecting the paper to conjugated polymers, OMIECs, OPVs, OECTs, OFETs, OPDs, materials AI, Bayesian optimization, self-driving labs, or experimental materials screening.

🔗 **Paper:** Direct DOI or official publisher link

🤖 **Materials-AI pick**

📄 **Paper title**

**Journal and year:** Journal, Year
**Category:** [one or two Track 2 categories]

**Short summary:**
Concise sentences on the research question, methodology, and main finding.

**Why it matters to my research:**
One sentence connecting the paper to materials AI, Bayesian optimization, active learning, self-driving labs, experimental screening, conjugated polymers, or organic electronics.

🔗 **Paper:** Direct DOI or official publisher link
```

If one track has no suitable verified unsent paper, retain the other paper block and put the applicable exact sentence in the missing slot:

`No suitable verified unsent polymer formulation/conformation paper was found today.`

`No suitable verified unsent materials-AI paper was found today.`

If neither track has a suitable paper, deliver exactly:

`[SILENT]`

Do not include research notes, status reports, or error messages in the delivered output.

## Verification

A cycle is complete only when:

- Each selected paper is a real peer-reviewed journal article published 2024-01-01 or later.
- The two selections are distinct and fill the two required tracks; if a track is missing, its exact missing-track sentence is used.
- Title, journal, year, DOI, and link are verified against a real source for each selection.
- Each selection passes every exclusion and is not simulation-only.
- Neither selection's DOI/normalized title was already in `paper-sent.json`.
- `paper-sent.json` remains valid JSON after appending.
- The delivered message is under 400 words and matches the required two-track format, or is exactly `[SILENT]` when neither track succeeds.

## Pitfalls

- Preprints (arXiv, ChemRxiv) do not qualify when a peer-reviewed journal version exists — find and cite the journal version.
- A DOI resolving to a real publisher page is the strongest existence check; verify the DOI resolves before sending.
- Do not rely on Scholar snippets alone; confirm the abstract and metadata on the publisher/DOI page.
- Publication year on the record (not the "available online" date) determines the 2024 cutoff; count early-access 2025 articles as 2025.
- Simulation-only papers (MD/DFT/MC/FEM/CFD) are excluded even when the topic is a conjugated polymer.
