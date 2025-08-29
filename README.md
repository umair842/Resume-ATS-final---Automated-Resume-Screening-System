# 📄 Resume ATS (final) — Automated Resume Screening System

This n8n workflow automates resume screening using AI and integrates with Microsoft Outlook, Google Drive, and Airtable to create a mini Applicant Tracking System (ATS).

---

## 🚀 Purpose

To automatically:
- Fetch job applications (emails with resumes)
- Extract job titles from email subjects
- Classify resumes using OpenAI and job descriptions from Airtable
- Score candidates based on a strict rubric
- Upload resumes to Google Drive
- Log structured results in Airtable
- Filter by country (e.g., Netherlands)

---

## 🛠️ Tech Stack

- **n8n** for workflow automation
- **Microsoft Graph API** (email and attachments)
- **OpenAI GPT-4o** for resume scoring
- **ConvertAPI** for DOCX to PDF conversion
- **Google Drive** for resume storage
- **Airtable** for job descriptions and candidate records

---

## 🔁 Flow Overview

### 1. **Trigger: Daily at 19:30**
- Uses `Schedule Trigger` to start workflow execution.

### 2. **Fetch Mail Folders**
- Queries Outlook folders via Microsoft Graph API.

### 3. **Locate 'Sollicitates' Folder**
- Dynamically finds folder with job applications.

### 4. **Fetch Recent Emails**
- Collects emails with attachments from the last 24 hours.

### 5. **Filter: Has Attachments**
- Checks for attachments in the emails.

### 6. **Extract Job Title**
- Parses job title from the email subject.

### 7. **Validate Job Title**
- Uses OpenAI to map subject to predefined job titles.
- If not matched, ends the flow for that email.

### 8. **Fetch Job Description**
- Looks up the matching job description in Airtable.

### 9. **Get Attachment**
- Downloads resume attachment from Outlook.

### 10. **Convert to PDF (if DOCX)**
- Uses ConvertAPI to convert to PDF if not already.

### 11. **Extract Resume Text**
- Reads the PDF resume content.

### 12. **Score Resume (OpenAI GPT-4o)**
- Sends resume + job description to AI for scoring.
- JSON response includes:
  - `score`
  - `summary`
  - `job_title`
  - `email`, `phone_number`, `country`

### 13. **Postprocess Results**
- Parses and structures the AI response.
- Merges it with the file and metadata.

### 14. **Filter: Country == Netherlands**
- Continues only if the candidate is from the Netherlands.

### 15. **Filter: Score ≥ 50**
- Only upload and log strong resumes.

### 16. **Upload to Google Drive**
- Uploads resume file to **"Sollicitaties-all"** folder.

### 17. **Create Airtable Record**
- Saves:
  - Name
  - Resume link
  - Email, Phone, Country
  - Score and Summary
  - Job Title

---

## 📊 Scoring Rubric (100 pts)

| Criteria                          | Points |
|----------------------------------|--------|
| Education                        | 20     |
| Tools, Certifications, Tech      | 20     |
| Relevant Work Experience         | 20     |
| Skills Alignment                 | 20     |
| Overall Quality + Alignment      | 20     |
| **Penalty** for vagueness/missing info | Yes |

**Note:** Max score is **80** unless the resume is exceptional.

---

## 📁 External Services Used

| Service           | Usage                                  |
|------------------|-----------------------------------------|
| Microsoft Outlook| Fetches emails and attachments          |
| OpenAI GPT-4o    | AI-based resume screening               |
| Airtable         | Stores job descriptions and results     |
| ConvertAPI       | Converts DOCX to PDF                    |
| Google Drive     | Uploads filtered resumes                |

---

## ✅ Requirements

- Connected credentials for:
  - Microsoft Outlook (OAuth2)
  - OpenAI
  - Airtable (Personal Access Token)
  - ConvertAPI
  - Google Drive (OAuth2)

---

## ⚠️ Notes

- Ensure job titles in Airtable are precise.
- Resumes must be PDF or DOCX (convertible).
- Email subject must clearly indicate the job title.
- Workflow supports only candidates from **Netherlands**.
- AI scoring is intentionally strict.
