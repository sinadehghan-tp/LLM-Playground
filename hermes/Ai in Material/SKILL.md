---
name: paper-finder
description: Find and deduplicate the single most relevant recent conjugated-polymer / materials-AI journal paper.
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

Each day, find and deliver the single most relevant peer-reviewed journal paper (published 2024-01-01 or later, prioritizing 2025+) for an early-career Materials Science PhD combining AI/ML with experimental conjugated-polymer and organic-electronics research. Verify every detail against a real source, never fabricate, and never send the same paper twice.

## When to Use

- Run the daily scheduled paper-scout cycle.
- Use when the user asks for a fresh relevant paper or to review a candidate.
- Use when adjusting the recurring Telegram paper scout.
- Do not use for job searches (that is the separate `job-finder` skill and `job_finder` directory).

## Working Directory and State

- The scheduled job pins its workdir to `D:\PhD_Code\LLM-Playground\hermes\Ai in Material`, keeping code, instructions, and deduplication state isolated from other Telegram projects.
- Maintain `paper-sent.json` in this directory: a JSON array of every previously sent paper.
- Telegram destination: the "Ai in Material" channel, chat ID `-1003543452515`.

## Research Priorities (in order)

1. Conjugated-polymer formulation, solution conformation, aggregation, self-assembly, solubility, and processing–structure relationships. Highly relevant even without AI/ML.
2. AI and machine learning for conjugated polymers and organic electronic materials.
3. Organic mixed ionic–electronic conductors (OMIECs): formulation, morphology, conformation, aggregation, processing, data-driven design.
4. Applications of conjugated polymers / organic semiconductors in OPVs, OECTs, OFETs, OPDs — especially studies linking formulation, processing, conformation, aggregation, or morphology to device performance.
5. Self-driving laboratories, autonomous experimentation, robotic labs, closed-loop materials discovery.
6. Bayesian optimization and active learning for experimental design or materials optimization.
7. Systems combining AI/scientific/LLM agents with Bayesian optimization and laboratory automation.
8. High-throughput experimental screening and ML pipelines for materials discovery.
9. Model development and statistical methodology: Gaussian processes, graph neural networks, uncertainty quantification, out-of-distribution generalization, kernel methods, interpretable models.

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

- `conjugated polymer aggregation formulation`, `conjugated polymer solution conformation`, `processing structure conjugated polymer`
- `machine learning conjugated polymer`, `machine learning organic electronic materials`
- `OMIEC morphology`, `mixed ionic electronic conductor formulation`
- `organic electrochemical transistor morphology`, `organic photovoltaic formulation morphology`, `OFET film morphology processing`, `organic photodetector`
- `self-driving laboratory materials`, `autonomous experimentation closed loop`
- `Bayesian optimization materials`, `active learning experimental design`
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
5. Apply exclusions and rank by the priority order. Break ties toward more recent, more directly conjugated-polymer/OMIEC/organic-electronics relevant work.
6. Select only the single best paper not already in `paper-sent.json`.
7. Append the selected paper to `paper-sent.json` atomically before/at delivery; keep the file valid JSON.

## Deduplication Registry

`paper-sent.json` is a JSON array; each entry has:

- `doi`
- `title`
- `journal`
- `year`
- `url`
- `date_sent`
- `category`

Rules:

1. Identity is `doi` when available; otherwise normalized title (lowercased, punctuation/whitespace collapsed).
2. Preserve existing entries; append the new one and write atomically.
3. Never select a paper whose DOI or normalized title already appears.
4. URL tracking parameters or formatting differences do not create a new paper.

## Telegram Output Format

Deliver exactly one message, under 200 words total, plain scientific language, no exaggerated claims. Format:

```text
📄 **Paper title**

**Journal and year:** Journal, Year
**Category:** [one or two categories from the priorities]

**Short summary:**
Concise sentences on the research question, methodology, and main finding.

**Why it matters to my research:**
One sentence connecting the paper to conjugated polymers, OMIECs, OPVs, OECTs, OFETs, OPDs, materials AI, Bayesian optimization, self-driving labs, or experimental materials screening.

🔗 **Paper:** Direct DOI or official publisher link
```

If no suitable verified paper can be found, deliver exactly:

`No sufficiently relevant verified paper published since 2025 was found today,`

Do not include research notes, status reports, or error messages in the delivered output.

## Verification

A cycle is complete only when:

- The selected paper is a real peer-reviewed journal article published 2024-01-01 or later.
- Title, journal, year, DOI, and link are verified against a real source.
- It passes every exclusion and is not simulation-only.
- Its DOI/normalized title was not already in `paper-sent.json`.
- `paper-sent.json` remains valid JSON after appending.
- The delivered message is under 200 words and matches the required format, or is the exact no-results sentence.

## Pitfalls

- Preprints (arXiv, ChemRxiv) do not qualify when a peer-reviewed journal version exists — find and cite the journal version.
- A DOI resolving to a real publisher page is the strongest existence check; verify the DOI resolves before sending.
- Do not rely on Scholar snippets alone; confirm the abstract and metadata on the publisher/DOI page.
- Publication year on the record (not the "available online" date) determines the 2024 cutoff; count early-access 2025 articles as 2025.
- Simulation-only papers (MD/DFT/MC/FEM/CFD) are excluded even when the topic is a conjugated polymer.
