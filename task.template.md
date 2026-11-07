# Job Search Agent Task (template)

Copy this file to `task.md` and replace everything in <ANGLE BRACKETS>.
Read this whole file before doing anything. Then read `profile.md` and the resume PDF in this folder.
Also read `applied.csv`, `applied_history.csv` and `rejections.csv` if they exist.
Follow the phases in order and stop where this file says to stop.

---

## 0. Ground rules (apply to every phase)

- You are working in my real Chrome through the Playwright MCP extension.
- ONLY use company careers pages and their job systems (Greenhouse, Lever, Ashby, Workday, SuccessFactors, SmartRecruiters, Workable, Teamtailor, company sites).
- NEVER use job boards through my logged-in browser. The only exception is the optional read-only scan in section 7, which never uses my logged-in browser.
- NEVER click Submit, Apply, Send, Finish or any button that sends an application. I click Submit myself.
- NEVER create an account, sign in or enter a password. If a site needs an account, stop and tell me.
- NEVER invent experience, skills, references, dates, grades or job titles. If it isn't in profile.md or my resume, ask me.
- If a field changes that you did not change, or a dialog appears that you did not open, STOP and ask me.
- Open only one application form at a time. I won't type in a tab while you're working in it.
- If a site shows a CAPTCHA or bot check, stop and tell me. Do not try to get around it.
- Keep updates to one short line per company while searching.

---

## 1. PHASE 1: Search

### 1.1 Companies to check
<LIST OF COMPANIES>

Tip: many companies use Greenhouse (`boards-api.greenhouse.io/v1/boards/<slug>/jobs`), Lever (`api.lever.co/v0/postings/<slug>`) or Ashby. Reading those public feeds is faster than clicking through pages. Use them when they work.
If you can't find a company's careers page within 2 minutes, skip it and note "no careers page found".

### 1.2 Find new companies (every run)
Also look for companies in <CITY / INDUSTRY, e.g. Melbourne AI and tech companies with 50 to 500 staff> that are NOT in 1.1. Use careers pages, public ATS feeds and "about us" or news pages. Check their open roles like any other company, then list them separately in the report so I can decide whether to add them to 1.1. Don't add them to this file yourself.

### 1.3 Time window
- Main list: roles posted or updated in the last 7 days.
- Near misses: strong fits posted 8 to 21 days ago, listed separately.

### 1.4 Target roles
<ROLE TYPES AND LEVELS, e.g. junior/graduate software engineer, AI engineer, data analyst, technical support engineer>

Internships: include them only if the ad says graduates or recent graduates can apply. Always skip internships that say "current students only", "penultimate year" or otherwise require enrolment. Put length, pay and any path to a permanent role in red flags.

### 1.5 Skip rules (exclude, but count them in the log)
Skip a role if ANY of these are true:
- <WORK-RIGHTS RULES, e.g. requires citizenship, permanent residency or security clearance>
- <EXPERIENCE RULES, e.g. asks firmly for 2+ years, or title says Senior/Lead/Staff/Manager>
- <ROLE-TYPE RULES>
- <LOCATION AND TRANSPORT RULES, e.g. needs a car or driver's licence>
- <CONTRACT RULES, e.g. fixed-term contracts that run past my work-rights end date>
- It's already in applied.csv, applied_history.csv or rejections.csv (same company, same or similar role).
- The company has a reapply lockout that covers this role (note these in profile.md).

If I'm willing to relocate, include roles in other cities and start their red flags with "relocation needed (City)".

### 1.6 Live check (every role)
Open every job link and confirm the posting is still open on the company's own site before adding it. Drop anything closed, expired or "no longer advertised". Aggregator listings go stale fast.

### 1.7 For every role that passes, record
Company, title, location, work mode, posted date, link, job system, salary (if shown), exact work-rights wording, exact experience wording, skills matched, skills missing, whether a cover letter is needed, visible application questions, and the source (careers page, job board scan, etc.).

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
- `jobs.csv`: add new rows (don't overwrite existing ones). Columns: company, title, location, work mode, posted date, link, job system, salary, score, why it fits, skills missing, red flags, cover letter needed, status, source.
- `search_log.md`: per company or source, the URL checked, roles seen, and how many were skipped and why.

### 2.3 STOP
Show a short table (rank, company, role, location, posted, score, link, work-rights wording, main red flag). Then list near misses, then any new companies found, then every company name in one line (so I can check my email for past applications to them). Wait. Do not open any application until I name the job.

---

## 3. PHASE 3: Prepare an application (only for the job I name)

0. Duplicate check: tell me the company name so I can search my email history for past applications. Trackers can be incomplete, and some companies lock you out of reapplying for months.
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
<TABLE OF LOCATION OR ROLE TYPE TO RESUME FILE, e.g. standard resume, relocation version, data-focused version>

---

## 5. Form rules (standard answers)
<YOUR STANDARD ANSWERS: contact details, links, education, experience level, notice period, salary range, declarations>

Work history fields: use the experience bullets in profile.md word for word. Don't shorten, reword or tailor them.

Always STOP and ask me about:
- visa sponsorship questions
- health, injury or adjustment declarations
- anything not covered here

---

## 6. Logging outcomes
When I tell you about a rejection, update the matching row in applied.csv or applied_history.csv to "unsuccessful" with the date. Never delete rows. If no row exists, add one.

---

## 7. Optional: job board read-only scan (ONLY when I say "board scan")

Some roles are only advertised on job boards. This section reads public listing pages without touching my account. Check each site's terms of use before enabling this, and turn it off if the site doesn't allow automated access.

### 7.1 Hard rules
- NEVER use my logged-in browser. Use WebFetch, or a separate fresh browser with no login and no saved cookies.
- Never sign in, apply, save jobs or touch my account on the site in any way.
- At most once per day. Check search_log.md first; if a scan already ran today, stop and tell me.
- At most <N, e.g. 10> page loads per run, with at least 10 seconds between loads.
- If the site shows a CAPTCHA, a "verify you are human" page, an access-denied page or any error, STOP immediately and tell me. Do not retry, switch tools or work around it.

### 7.2 Search
- Sites: <JOB BOARD(S)>
- Location: <LOCATION>. Listed in the last <N> days.
- Keywords (one search each, first results page only): <KEYWORDS>
- Use the search-result snippets first; only open an ad when a snippet can't settle a skip rule.

### 7.3 Filter, link and save
- Apply the same skip rules as Phase 1.
- For each role that passes, look for the same job on the company's own careers page and use that link if it exists (applying direct is better). Otherwise use the board link and say so.
- Add results to jobs.csv with source "<BOARD> scan", log the run in search_log.md (loads used, results per keyword, skips and why, errors), show me the table and stop.

---

## 8. Final reminder
Search, score, save, then STOP. For applications: fill, check, then STOP. The human always clicks Submit.
