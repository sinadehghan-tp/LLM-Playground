---
name: job-finder
description: Find and deduplicate matching scientific jobs.
version: 0.1.0
author: sinad, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [jobs, materials-science, machine-learning, linkedin]
    related_skills: []
---

# Scientific Job Finder

Search for recent scientific roles that fit an early-career Materials Science PhD combining AI/ML with experimental materials research. Verify every alert against the original employer posting, use LinkedIn as a mandatory discovery source, and persist deduplication state.

## When to Use

- Run every scheduled scientific job-search cycle.
- Use when the user asks for matching jobs, internships, or a review of a specific posting.
- Use when adjusting the recurring Telegram job scout.
- Do not use for general software, business, management, postdoctoral, co-op, non-US, or national-laboratory searches.

## Candidate Profile

Strongest intersection:

- AI/ML for materials, materials informatics, property prediction, Gaussian processes, Bayesian optimization, active learning, Design of Experiments, uncertainty calibration, and OOD analysis.
- Python, scikit-learn, PyTorch, RDKit, Git, HPC/LSF, scientific datasets, and data pipelines.
- Automated liquid handling integrated with UV-Vis and DLS for experimental screening and formulation optimization.
- Conjugated polymers, organic electronics, polymer formulation, polymer-solvent interactions, Hansen parameters, solution processing, and high-throughput formulation.
- DLS, SLS, SANS, SAXS, UV-Vis, SEC-MALS, GPC, FTIR, DSC, rheology, mechanical characterization, composites, extrusion, additive manufacturing, and degradation.

## Hard Filters

Apply these before scoring:

1. Include only jobs physically located in the United States. A remote role qualifies only when explicitly open to US-based workers.
2. Include only postings with a verified publication date in the preceding seven calendar days.
3. Include internships, especially PhD, graduate, R&D, research, materials, polymer, formulation, characterization, automation, data-science, and ML internships.
4. Exclude co-ops when the public title or substantive description calls the role a co-op.
5. Allow an explicit internship even if its ATS uses a generic `Co-Op Student` worker category.
6. Exclude all postdoctoral/postdoc positions and postdoctoral-style fellowships, scholars, appointees, or research-associate roles.
7. Exclude national-laboratory jobs, national-lab contractors, and roles based at a national laboratory.
8. Exclude senior, staff, principal, director, management, and generic AI/LLM/software/data/business/manufacturing/production roles.

Never alert on a role that fails a hard filter.

## Priority Roles

Prioritize:

- Materials AI/ML and materials informatics.
- Autonomous or self-driving laboratories.
- High-throughput experimentation and laboratory automation.
- Data-driven materials R&D.
- Polymer, formulation, organic-electronics, and electronic-materials R&D.
- Polymer characterization and materials testing.
- Related chemicals, semiconductors, batteries, membranes, coatings, adhesives, biomaterials, and composites when transferability is concrete.
- Early-career Scientist, Research Scientist, Materials Scientist, materials-focused ML Scientist/Engineer, Research Engineer, R&D Scientist, and internships.

A strong role normally combines at least two of materials, ML/AI, scientific data, automation, high-throughput experimentation, polymers, formulation, or characterization.

## Discovery Sources

Use all five source groups every cycle.

### LinkedIn Jobs

LinkedIn is mandatory, not an optional afterthought. Search public LinkedIn Jobs for US postings from the past week, sorted newest, across distinct queries:

- `materials AI ML`
- `materials informatics`
- `Bayesian optimization materials`
- `autonomous laboratory`
- `self-driving laboratory`
- `high throughput experimentation`
- `polymer scientist`
- `polymer formulation`
- `materials characterization`
- `organic electronics scientist`
- `materials R&D internship`
- `polymer internship`
- `R&D materials internship`
- `PhD materials internship`

Use `web_search` with targeted `site:linkedin.com/jobs/view` queries and `web_extract` on exact public pages. Preserve canonical URLs of the form `https://www.linkedin.com/jobs/view/<numeric-id>/`. Never use a company profile, search URL, or different requisition as the LinkedIn job link.

### Indeed

Search public Indeed job pages every cycle with the priority role families and US/date filters. Use targeted `site:indeed.com/viewjob` discovery when direct Indeed search is blocked or noisy. Treat Indeed as a discovery source: verify the role, posting date, location, and active status on the original employer site before scoring or alerting, and never substitute an Indeed URL for the required employer application URL.

### Employer and ATS Sites

Always scan recent relevant listings at:

- 3M
- Dow
- DuPont
- BASF
- Corning
- Michelin
- Apple materials R&D
- Lila Sciences
- Corteva Agriscience

Rotate additional chemical, materials, semiconductor, battery, coatings, adhesives, membrane, and biomaterials employers. Search both full-time and internship families.

### Google Careers

Search Google Careers every cycle at `https://www.google.com/about/careers/applications/jobs/results` for US roles matching the priority materials, AI/ML, scientific-computing, automation, and internship families. Verify candidates on their exact Google Careers job pages and apply the same recency, location, level, and fit requirements as every other employer posting.

### Broad Web Search

Use `web_search` for the priority role families, then prefer the original employer application page. Aggregators may support discovery but are never the final application URL.

## Dynamic Workday Pages

A Workday page that extracts as only `Loading` is not evidence that the job is missing or inactive. Query the public CXS JSON API with `terminal` and `curl`:

1. POST `https://TENANT.wdN.myworkdayjobs.com/wday/cxs/TENANT/SITE/jobs` with JSON containing `appliedFacets`, `limit`, `offset`, and `searchText`.
2. Read `externalPath` values from the response.
3. GET `https://TENANT.wdN.myworkdayjobs.com/wday/cxs/TENANT/SITE` plus the selected `externalPath`.
4. Verify `posted`, `canApply`, `startDate`, US location, requisition ID, and the complete description.

Mandatory Workday checks:

- 3M: query `https://3m.wd1.myworkdayjobs.com/wday/cxs/3m/Search/jobs` with `materials`, `AI ML`, `formulation`, `scientist`, and `internship`.
- Michelin: query `https://michelinhr.wd3.myworkdayjobs.com/wday/cxs/michelinhr/Michelin/jobs` with `materials`, `R&D`, and `internship`.
- Corteva: query `https://corteva.wd5.myworkdayjobs.com/wday/cxs/corteva/Corteva/jobs` with `materials`, `formulation`, `polymer`, `R&D`, and `internship`.

Regression examples that this process must be capable of discovering without supplied URLs:

- 3M requisition `R01170221`, LinkedIn job `4460749238`.
- Michelin requisition `R-2026033245`, LinkedIn job `4460925520`.
- Corteva requisition `248125W`, LinkedIn job `4460410014`.

## Search Bounds

- Make at most 16 `web_search` and 22 `web_extract` calls per cycle.
- Batch independent searches.
- Direct ATS API requests may supplement these limits.
- Never loop retries on an empty or failed source.
- If a job cannot be verified, exclude it rather than guessing.

## Fit Scoring

Score only after hard filters:

- 30 points: AI/ML and materials-informatics relevance.
- 20 points: experimental materials relevance.
- 15 points: automation, high-throughput, or self-driving-lab relevance.
- 15 points: polymer, formulation, or characterization relevance.
- 10 points: early-career PhD alignment.
- 10 points: technical-skill transferability.

Alert on every verified new role scoring at least 50:

- 85-100: Excellent Match.
- 75-84: Strong Match.
- 50-74: Good Match.

Rank the strongest matches first. Mention explicit citizenship, security-clearance, permanent-residency, enrollment/major, and visa-sponsorship conditions.

## Persistent Deduplication

Maintain `materials-ai-polymer-sent.json` in this job finder's working directory. The scheduled job pins its workdir to this directory, keeping its code, instructions, and deduplication state isolated from other Telegram projects and schedulers.

The registry is a JSON array with these core fields:

- `company`
- `title`
- `job_id`
- `url`
- `date_found`
- `fit_score`
- `sent_to_telegram`

Add optional `linkedin_url` when verified.

Deduplication rules:

1. Use `job_id` as identity when available; otherwise use normalized company + title + URL.
2. Preserve existing rows and update the JSON atomically.
3. Never alert on a matching row whose `sent_to_telegram` is true.
4. URL tracking parameters or formatting changes do not create a new job.
5. Treat a repost as new only when it has a genuinely different requisition ID.
6. Record reviewed but unselected roles with `sent_to_telegram: false`.
7. Set `sent_to_telegram: true` only when the selected role is included in the Telegram-bound final output.
8. Keep previously sent excluded jobs as historical records, but never resend them.

## Telegram Output

Return exactly `[SILENT]` when no verified new role qualifies or when a blocking research error prevents verification. Otherwise return only one or more blocks separated by `---`:

```text
🎯 **[SCORE]/100 — [Excellent / Strong / Good Match]**

**[Job Title]**
🏢 [Company]
📍 [US location]
📅 Posted: [verified date]

**Why it matches me:**
• [specific fit]
• [specific fit]
• [optional third fit]

**Main relevant skills:**
[3-6 overlapping skills]

⚠️ **Potential gap:** [most important gap or "No major gap identified"]

🔗 **Apply (Company):** [direct active employer URL]
💼 **LinkedIn:** [exact active LinkedIn job URL when verified; otherwise omit]
```

Do not include introductions, research notes, errors, status reports, or no-results explanations in Telegram-bound output.

## Procedure

1. Read the persistent registry and build the known-job identity set. Completion: every existing row is available for deduplication.
2. Search LinkedIn, Indeed, Google Careers, direct ATS feeds, employer sites, and the broad web within the stated bounds. Completion: every mandatory source group was queried.
3. Resolve promising LinkedIn results to original employer postings. Completion: each candidate has an exact LinkedIn URL when available and a direct employer URL.
4. Read and verify the actual description, status, date, location, level, and role type. Completion: every candidate passes or is rejected by each hard filter.
5. Score transferable fit from the description rather than the title alone. Completion: each retained candidate has a defensible 0-100 score.
6. Deduplicate and update the registry. Completion: no previously sent identity is selected twice.
7. Produce only the required Telegram blocks or `[SILENT]`. Completion: every alert has a direct employer URL and an optional exact LinkedIn URL.

## Pitfalls

- Workday HTML commonly renders as `Loading`; use CXS JSON.
- LinkedIn search links containing `currentJobId` are discovery links, not canonical job links. Convert them to `/jobs/view/<id>/`.
- Some ATS systems label internships as `Co-Op Student`; trust the public title and substantive description when both explicitly say internship.
- Academic PhD research can satisfy experience stated as academic, private, public, government, or military experience.
- Do not assume visa eligibility; quote explicit restrictions briefly.

## Verification

A cycle is complete only when:

- Every alert is active, US-based, no older than seven days, not a postdoc, not a co-op, and not a national-lab role.
- Every score is at least 50.
- Every job has a direct employer application URL.
- Every applicable LinkedIn URL refers to the same requisition.
- No selected job was previously marked `sent_to_telegram: true`.
- The registry remains valid JSON after updating.
