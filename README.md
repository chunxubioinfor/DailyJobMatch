<img width="90" height="90" alt="ChatGPT 2025-11-24 17 16 20" src="https://github.com/user-attachments/assets/8c3a55fa-c731-4dd7-9fdd-edfce1430818" />

# DailyJobMatch  
![Made with n8n](https://img.shields.io/badge/Made%20with-n8n-00e8a2?style=flat&logo=n8n) 
![Powered by OpenAI](https://img.shields.io/badge/Powered%20by-OpenAI-412991?style=flat&logo=openai)
![Docker](https://img.shields.io/badge/Run%20with-Docker-2496ED?style=flat&logo=docker)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat)

> **Automated AI-powered job-matching workflow built with n8n**

---

## 📑 Table of Contents
1. [Overview](#-overview)  
2. [Architecture Diagram](#-architecture-diagram)  
3. [How to Use](#-how-to-use-dailyjobmatch)  
   - [Install n8n](#1️⃣-installing--running-n8n-with-docker)  
   - [Set Up Credentials](#2️⃣-setting-up-required-credentials)  
   - [Import Workflow](#3️⃣-importing--configuring-the-workflow)  
4. [Output Format](#-output-format)  
5. [Example Email](#-example-email-output)  
6. [Contact](#-contact)  
7. [Roadmap](#-roadmap)

---

## 📚 Overview

### 🧠 What DailyJobMatch Does  
DailyJobMatch automates your job search every morning by collecting fresh job postings, comparing each role against your CV using a large language model, and emailing you a ranked shortlist of the most relevant opportunities.

### ⭐ Core Features
- 🔄 **Daily automation**: Scheduled trigger (e.g. 07:30) runs the entire pipeline without manual effort.
- 📝 **CV-aware matching**: Uses the full text of your CV, not just keywords, to evaluate fit. 
- 💯 **Multi-dimensional scoring**: Breaks fit into background match, skills overlap, experience relevance, seniority, language requirements, and company score.
- 🧹 **Cleaning & deduplication**: Normalises fields, removes obvious mismatches (student roles, internships, postdocs), and deduplicates by title/company/link.
- 📊 **Structured LLM output**: Forces the model to return strict JSON, then parses and validates it before ranking. 
- 🥇 **Top-N selection**: Ranks all jobs by overall score and keeps only the top matches for you to review.  
- 💌 **Dark-theme email report**: Sends a dark-theme HTML digest with job cards, scores, keywords, fit bullets, and “View & Apply” buttons.

---

## 🏗️ Architecture Diagram

<p align="center">
  <img width="2268" height="386" alt="image" src="https://github.com/user-attachments/assets/7ceafeef-1611-4131-9f8b-03a719967199" />
  <br/>
  <img width="2565" height="406" alt="image" src="https://github.com/user-attachments/assets/5b406f69-ec06-4b07-aae6-ad858304abda" />
</p>

---

## 📘 How to Use DailyJobMatch

> This is an early-stage project and still requires personalised setup.

---

### 1️⃣ Installing & Running n8n with Docker  
DailyJobMatch is built on **n8n**, a flexible automation tool. Docker is the recommended and easiest deployment method.
Here is an installation guide: https://github.com/n8n-io/n8n

---

### 2️⃣ Setting Up Required Credentials  

DailyJobMatch relies on several external services:

#### 🔹 Google Drive & Gmail  
Used to fetch your CV and send daily job digest emails. They require the **Google credentials**, and here are the official docs from n8n:
https://docs.n8n.io/integrations/builtin/credentials/google/

#### 🔹 Apify (LinkedIn Job Scraper)  
In our workflow, [Apify](https://console.apify.com) is used to collect fresh LinkedIn jobs within the last 24 hours. Apify provides tons of actors to scrape up-to-date web data from any website for AI apps and agents, and I chose [**_Linkedin Jobs Scraper - PPR_**](https://apify.com/curious_coder/linkedin-jobs-scraper) as it is paid by result rather than subscription. But feel free to choose the scrapper which fits your needs, and the detailed configuration steps will be on the actor's page. 

#### 🔹 OpenAI (LLM scoring model)  
DailyJobMatch uses an LLM to read your job description, read your CV, evaluate the matches and score the fit across 6 dimensions and finally generate structured JSON outputs. n8n can be integrated with almost all the conversational chatbots. Here's how you can set up with the **OpenAI model**:
https://docs.n8n.io/integrations/builtin/credentials/openai/

---

### 3️⃣ Importing & Configuring the Workflow  
After setting up Docker and credentials, import:

```
workflow/Daily_Job_Match.json
```

> ⚠️ **Replace all my example credentials with your own.** 

I've written a few lines of descriptions for each node, and feel free to click and check the official docs to learn more. Here I will just walk through these key nodes, which you may need to alter or customise based on your needs:

- **Config**: Add Apify API key (NOT the full link, check LinkedIn node), recipient email, and number of jobs per day.  
- **RetrieveCV (Google Drive)**: Under Credentials, select your Google Drive OAuth2 credential and make sure the file / fileId is set to your CV PDF.  
- **LinkedIn** Under URL or body, make sure it uses your Apify dataset or actor endpoint (depending on how you configured it). Under Authentication / Headers, ensure your Apify API token is set. 
- **Filter & Deduplicate**: Update banned keywords (e.g., “student”, “temporary”).  
- **Score Job & Extract**: customise the prompt for your agent, including the goals, input and tasks.  
- **Model Nodes**: finish credentials setting and choose your model. 
- **Send a Message (Gmail)**: Under Credentials, choose your Gmail OAuth2 credential. Ensure the To field is either A static email or an expression pointing to Config.gmailTo.

#### ✔️ Manual Test  
Disable schedule → click **Execute Workflow** → review step-by-step execution.

---

## 📤 Output Format

For my personalised agent, the scoring node requires the LLM to:

- Read your entire CV  
- Read the job description  
- Evaluate fit across six dimensions  
- Output strict JSON (no markdown or commentary)  
- Keep the structure consistent across all jobs  

### JSON Template
```json
{
  "score": {
    "overall": 0,
    "background_match": 0,
    "skills_overlap": 0,
    "experience_relevance": 0,
    "seniority": 0,
    "language_requirement": 0,
    "company_score": 0
  },
  "summary": "",
  "keywords": [],
  "fit_bullets": [],
  "connector": ""
}
```

### Scoring Rubric

| Category                  | Range | Description                                                        |
|---------------------------|-------|--------------------------------------------------------------------|
| **background_match**      | 0–10  | Fit with domain (bioinformatics, pharma, biotech)                  |
| **skills_overlap**        | 0–30  | Match between required skills and your technical stack              |
| **experience_relevance**  | 0–30  | Alignment with responsibilities & past projects                     |
| **seniority**             | 0–10  | Entry-level preference; penalises senior roles                      |
| **language_requirement**  | 0–10  | Penalises strict “Danish required” listings                         |
| **company_score**         | 0–10  | Bonus for biotech/pharma/AI companies                               |
| **overall**               | 0–100 | Sum of all categories                                               |

---

## 📧 Example Email Output

<p align="center">
  <img src="https://github.com/user-attachments/assets/30da81d5-ba15-43fc-8345-aebe2c4ac42c" width="500">
</p>

---

## 📬 Contact

- **Author:** Chunxu Han  
- **Email:** s220311@dtu.dk  
- **LinkedIn:** https://www.linkedin.com/in/chunxu-han  

Feel free to reach out with questions or feature ideas!

---

## 🌟 Roadmap

- JobIndex integrations  
- Notion job-tracking storage  
- Auto-apply system (draft cover letters) 
- Weekly analytics dashboard
- AI-based CV improvement suggestions 

_Contributions welcome — open an issue or submit a PR!_

---
