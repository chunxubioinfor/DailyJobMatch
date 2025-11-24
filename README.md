# ✨ DailyJobMatch

> **Automated AI-powered job-matching workflow built with n8n**

## Overview
### 🧠 What DailyJobMatch does

DailyJobMatch automates your job search by running every morning, collecting fresh job postings, matching each role against your CV using a large language model, and then sending you a ranked shortlist of the most relevant positions directly to your inbox.

### ⭐ Core features

- 🔄 **Daily automation**: Scheduled trigger (e.g. 07:30) runs the entire pipeline without manual effort.
- 📝 **CV-aware matching**: Uses the full text of your CV, not just keywords, to evaluate fit.
- 💯 **Multi-dimensional scoring**: Breaks fit into background match, skills overlap, experience relevance, seniority, language requirements, and company score.
- 🧹 **Job cleaning & deduplication**: Normalises fields, removes obvious mismatches (student roles, internships, postdocs), and deduplicates by title/company/link.
- 📊 **Structured LLM output**: Forces the model to return strict JSON, then parses and validates it before ranking.
- 🥇 **Top-N selection**: Ranks all jobs by overall score and keeps only the top matches for you to review.
- 💌 **Nice email report**: Sends a dark-theme HTML digest with job cards, scores, keywords, fit bullets, and “View & Apply” buttons.

### 🏗️ Architecture Diagram

