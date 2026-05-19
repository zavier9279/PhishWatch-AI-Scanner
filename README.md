# PhishingWatch-AI-Automated 

![n8n workflow](docs/images/workflow.png)

Delivered a production-style anti-phishing automation that turns raw email content into an evidence-backed decision and a ready-to-share incident report — with minimal SOC effort.

## Problem solved
Phishing triage is usually slow and inconsistent: analysts must read emails, judge risk from wording, extract URLs, check VirusTotal, gather context, and then write both technical and executive summaries. This project automates that workflow to reduce triage time, standardize decisions, and produce consistent reports while keeping VirusTotal as the authoritative evidence source for URL verdicts.

## What the solution does (end-to-end)
### 1) Email → ML scoring (content-driven)
- The email (subject, sender, body) is normalized and scored using a DistilBERT phishing classifier deployed as a FastAPI microservice on Railway, integrated into n8n Cloud.
- Each input produces its own confidence/accuracy score (observed outputs reach ~99.6% and up to 100% on clear cases).

### 2) URL extraction → VirusTotal evidence
- Extracts URL(s) from the email body and queries VirusTotal for reputation and vendor detections to drive an evidence-first classification.

### 3) Zero-Day URL analysis (secondary check)
- If VirusTotal returns **legitimate/clean**, the AI IR agent performs an additional risk analysis to assess whether the URL shows **zero-day-like behavior** (not yet flagged by VirusTotal) and highlights suspicious patterns for analyst review.
- This step is used as **risk enrichment** and does not replace VirusTotal evidence for final reporting.

### 4) Hacker News enrichment (context)
- Adds up to 3 relevant security references from Hacker News for context (enrichment only — not used to decide the verdict).

### 5) Automated reporting (technical + non-technical)
Generates two aligned outputs:
- Technical Incident Response report grounded in VirusTotal evidence (with MITRE mapping when supported)
- Non-technical executive summary suitable for stakeholders
- The agent is supported with OpenAI chat model

### 6) Automation-ready output
Returns a structured JSON response containing:
- ML verdict + confidence
- Extracted indicators (URLs)
- VirusTotal evidence
- Zero-day risk analysis notes (when applicable)
- Optional Hacker News references
- Generated reports

This output is ready to plug into alerts, ticketing, or email notifications.

## Tech Stack
- n8n Cloud
- FastAPI / Uvicorn
- Railway
- Hugging Face Transformers (DistilBERT)
- VirusTotal API
- Hacker News enrichment
- Agentic reporting
