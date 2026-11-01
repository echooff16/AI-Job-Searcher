# Job Search Agent Task (template)

Copy this file to `task.md` and replace everything in <ANGLE BRACKETS>.
Read this whole file before doing anything. Then read `profile.md` and the resume PDF in this folder.
If `applied.csv` exists, read it too. Follow the phases in order and stop where this file says to stop.

---

## 0. Ground rules (apply to every phase)

- You are working in my real Chrome through the Playwright MCP extension.
- ONLY use company careers pages and their job systems (Greenhouse, Lever, Ashby, Workday, SuccessFactors, SmartRecruiters, Workable, company sites).
- NEVER use job boards such as SEEK, LinkedIn, Indeed or Glassdoor through the browser.
- NEVER click Submit, Apply, Send, Finish or any button that sends an application. I click Submit myself.
- NEVER create an account or enter a password. If a site needs an account, stop and tell me.
- NEVER invent experience, skills, references, dates, grades or job titles. If it isn't in profile.md or my resume, ask me.
- If a field changes that you did not change, or a dialog appears that you did not open, STOP and ask me.
- Open only one application form at a time.
- If a site shows a CAPTCHA or bot check, stop and tell me. Do not try to get around it.
- Keep updates to one short line per company while searching.

---

## 1. PHASE 1: Search

### 1.1 Companies to check
<LIST OF COMPANIES>

Tip: many companies use Greenhouse (`boards-api.greenhouse.io/v1/boards/<slug>/jobs`), Lever (`api.lever.co/v0/postings/<slug>`) or Ashby. Reading those public feeds is faster than clicking through pages. Use them when they work.
If you can't find a company's careers page within 2 minutes, skip it and note "no careers page found".

### 1.2 Time window
- Main list: roles posted or updated in the last 7 days.
- Near misses: strong fits posted 8 to 21 days ago, listed separately.

### 1.3 Target roles
<ROLE TYPES AND LEVELS, e.g. junior/graduate software engineer, AI engineer, technical support engineer>

### 1.4 Skip rules
Skip a role if ANY of these are true:
- <WORK-RIGHTS RULES, e.g. requires citizenship or security clearance>
- <EXPERIENCE RULES, e.g. asks for 2+ years, or title says Senior/Lead/Staff>
- <ROLE-TYPE RULES>
- <LOCATION AND COMMUTE RULES>
- <EMPLOYMENT-TYPE RULES>
- Is a role already in applied.csv.

### 1.5 For every role that passes, record
Company, title, location, work mode, posted date, link, job system, salary (if shown), exact work-rights wording, exact experience wording, skills matched, skills missing, whether a cover letter is needed, and visible application questions.

---

## 2. PHASE 2: Score and save

### 2.1 Scoring (out of 10)
- Role level fit: 0 to 3
- Skills match: 0 to 3
- Location and commute: 0 to 2
- Work-rights signals: 0 to 1
- Start date fit: 0 to 1

Give a one-line reason per score. Do not inflate scores.

### 2.2 Save
- `jobs.csv`: company, title, location, work mode, posted date, link, job system, salary, score, why it fits, skills missing, red flags, cover letter needed, status. Sort by score.
- `search_log.md`: per company, the URL checked, roles seen, and how many were skipped and why.

### 2.3 STOP
Show a short table (rank, company, role, location, posted, score, main red flag), then wait. Do not open any application until I name the job.

---

## 3. PHASE 3: Prepare an application (only for the job I name)

1. Open the form in a new tab.
2. Tell me which resume to upload (see section 4) and which box to use. You cannot attach files yourself.
3. Fill every other field from profile.md and the form rules.
4. Stop and wait for me to say "uploaded".
5. Re-check every field after the upload, because resume parsers often overwrite fields.
6. Leave free-text answers ("why this role") blank and show me the question.
7. Report every field filled, every field left blank and why, and any warnings. Then say "Ready for you to review and Submit."
8. After I confirm I've submitted, add a row to applied.csv.

---

## 4. Which resume to use
<TABLE OF LOCATION TO RESUME FILE>

---

## 5. Form rules (standard answers)
<YOUR STANDARD ANSWERS: contact details, links, education, experience level, notice period, salary bands, declarations>

Always STOP and ask me about:
- visa sponsorship questions
- anything not covered here

---

## 6. Final reminder
Search, score, save, then STOP. For applications: fill, check, then STOP. The human always clicks Submit.
