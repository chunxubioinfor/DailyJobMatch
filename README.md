# DailyJobMatch#

# Overview

### What DailyJobMatch does

DailyJobMatch automates your job search by running every morning, collecting fresh job postings, matching each role against your CV using a large language model, and then sending you a ranked shortlist of the most relevant positions directly to your inbox.

Concretely, the agent:

- Downloads your latest CV from Google Drive and extracts the text.
- Scrapes recent LinkedIn job postings via Apify for your chosen keywords and locations.
- Cleans, normalises, and filters the job data (removing students, postdocs, duplicates, etc.).
- Uses an LLM to score how well each job fits your background across several dimensions.
- Sorts jobs by overall fit and selects the top N (e.g. top 15).
- Generates a styled HTML email summarising the matches and sends it via Gmail.

---

### Core features

- **Daily automation** – Scheduled trigger (e.g. 07:30) runs the entire pipeline without manual effort.
- **CV-aware matching** – Uses the full text of your CV, not just keywords, to evaluate fit.
- **Multi-dimensional scoring** – Breaks fit into background match, skills overlap, experience relevance, seniority, language requirements, and company score.
- **Job cleaning & deduplication** – Normalises fields, removes obvious mismatches (student roles, internships, postdocs), and deduplicates by title/company/link.
- **Structured LLM output** – Forces the model to return strict JSON, then parses and validates it before ranking.
- **Top-N selection** – Ranks all jobs by overall score and keeps only the top matches for you to review.
- **Beautiful email report** – Sends a dark-theme HTML digest with job cards, scores, keywords, fit bullets, and “View & Apply” buttons.

---

### Architecture diagram

The workflow is implemented entirely in n8n and is composed of several logical stages:

1. **Schedule & Config** – Daily trigger and global settings (API keys, email, limits).
2. **CV Retrieval & Preprocessing** – Google Drive download → PDF text extraction → `cv_text`.
3. **Job Data Collection & Preprocessing** – LinkedIn scrape → normalization → filtering → deduplication → `job_meta` + `job_text`.
4. **AI Scoring & Parsing** – LLM agent compares `job_text` + `cv_text` → structured JSON → parsed and merged with metadata.
5. **Ranking & Selection** – Sort by `score.overall` and slice the top N jobs.
6. **Output & Notification** – Build HTML email and send via Gmail.

You can include the visual workflow diagram as:

```markdown
![DailyJobMatch architecture](docs/dailyjobmatch-architecture.png)
