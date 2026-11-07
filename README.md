# AI Job Search Agent

An agent workflow that uses **Claude Code** and the **Playwright MCP** to search company careers pages, filter roles against eligibility rules, score them against a resume, and prepare applications for human review.

I built this during my own graduate job search to replace hours of manual searching with a repeatable run. The agent finds, checks, ranks and pre-fills. A human always reviews and clicks Submit.

---

## What it does

| Phase | What happens | Output |
|---|---|---|
| 1. Search | Checks 50+ company careers pages. Reads public job feeds (Greenhouse, Lever, Ashby) where available instead of clicking through pages, and falls back to the browser for JavaScript-heavy sites. Also looks for new companies in the target city or industry and reports them separately. | Raw list of roles |
| 2. Live check | Opens every job link and confirms the posting is still open on the company's own site. Stale and closed listings are dropped. | Only live roles |
| 3. Filter and score | Applies skip rules (seniority, work-rights wording, location, contract length, role type, date window, already applied) and scores each remaining role out of 10 against the resume, with a one-line reason per score. | `jobs.csv`, `search_log.md` |
| 4. Prepare | For a role the user picks, runs a duplicate check, opens the form in the user's own Chrome, fills standard fields from a profile file, re-checks every field, then stops. | A filled form, ready for review |
| 5. Track | After the user confirms submission or an outcome, logs it. | `applied.csv` |

Optional extras:
- **Job board read-only scan** (section 7 of the template): reads public listing pages on a job board without using the user's account, with strict limits on page loads and an immediate stop on any bot check.
- **Scheduled inbox check** (`inbox_check.template.md`): a read-only prompt that runs a few times a day, summarises application updates and triages job alert emails into a short list of leads with odds ratings.

---

## How it works

```
task.md (rules, phases, scoring)  ─┐
profile.md (candidate details)    ─┼─► Claude Code ─► Playwright MCP ─► user's Chrome
resume.pdf                        ─┘        │
                                            ├─► jobs.csv        (ranked shortlist)
                                            ├─► search_log.md   (coverage per company)
                                            └─► applied.csv     (tracking)

inbox_check.md ─► scheduled AI task (read-only email) ─► updates + new leads ─► applied.csv
```

- **Instructions as a spec.** All behaviour lives in `task.md`: phases, skip rules, a scoring rubric, standard form answers and stop conditions. Changing the search means editing a text file, not code.
- **Public job feeds first.** Many companies use an applicant tracking system with a public JSON feed. Reading the feed is faster and more reliable than navigating pages.
- **Runs in the user's real browser.** Playwright MCP's extension mode connects to an existing Chrome profile, so pages behave as they would for a normal visitor.

---

## Guardrails (and why each one exists)

Each rule came from something that went wrong in real use.

| Guardrail | What happened without it |
|---|---|
| Never click Submit; stop on the final page | Submission is a one-way action. A human should always review first. |
| Re-check every field after a resume upload | A resume parser auto-filled the GitHub field with a portfolio URL. |
| Stop if a field changes that the agent didn't change | The user and the agent edited the same form at once and fields were overwritten. |
| Fill fields first, then ask the user to upload files | The browser extension is not allowed to attach local files, so uploads are done by the user. |
| Live-check every link on the company's own site | A role from an aggregator had already closed, and time was spent preparing an application for it. |
| Duplicate check against email history, not just the tracker | The tracker only went back a few months. One company had a 12-month reapply lockout from an earlier application, which only showed up at the final Submit. |
| Never use the logged-in browser on job boards | Automated activity on personal job-board accounts risks the account being flagged. The optional scan reads public pages logged out, with a page-load limit. |
| Time and volume limits on large sources | One aggregator returned over 1,000 listings, mostly stale, and a single check took 17 minutes. Sources now have a date window, a cap on roles verified and a time limit. |
| Stop at any CAPTCHA or bot check | Some systems reject automated activity. The agent should never try to get around them. |
| Always ask the user about visa, sponsorship and health declarations | These answers are personal and situational, so they should never be automated. |
| Never invent experience, skills or dates | Anything not in the profile or resume is left blank and flagged. Work history uses the profile's bullets word for word. |

---

## Files

| File | Public? | Purpose |
|---|---|---|
| `README.md` | Yes | This file |
| `task.template.md` | Yes | The agent spec with placeholders. Copy to `task.md` and fill in. |
| `profile.example.md` | Yes | Example candidate profile with fake details. Copy to `profile.md`. |
| `inbox_check.template.md` | Yes | Optional scheduled inbox check prompt. Copy to `inbox_check.md`. |
| `examples/jobs.sample.csv` | Yes | Example output format |
| `task.md`, `profile.md`, `inbox_check.md`, `applied*.csv`, `rejections*.csv`, `jobs.csv`, `search_log.md`, `*.pdf` | **No** | Real personal files. Excluded by `.gitignore`. |

---

## Setup (Windows)

1. Install [Node.js](https://nodejs.org) (LTS) and [Claude Code](https://code.claude.com/docs/en/setup).
2. Install the **Playwright MCP Bridge** extension in Chrome.
3. In a terminal, in this folder:

   ```
   claude mcp add playwright -e PLAYWRIGHT_MCP_EXTENSION_TOKEN=<your-token> -- npx @playwright/mcp@latest --extension
   ```
4. Copy `task.template.md` to `task.md` and `profile.example.md` to `profile.md`, then fill them in. Add your resume PDF.
5. Start `claude` and type:

   ```
   Read task.md and follow it.
   ```
6. When a session needs your logged-in tab (for example, to fill a form), open the tab in Chrome and connect it through the Playwright extension icon first.

---

## Limitations

- Some applicant tracking systems (for example, certain Lever and Workday configurations) reject submissions from automated browsers. The workflow is designed for the user to submit manually anyway.
- Careers pages change often. A company that can't be read in about two minutes is logged as skipped rather than retried.
- Job board search pages often load results with JavaScript, so a logged-out page fetch may not see them. Email job alerts plus the inbox check are a more reliable way to cover job boards.
- Scores are a starting point for human judgement, not a decision.

---

## What I learned

- Writing the agent's behaviour as a clear spec, with explicit stop conditions, mattered more than any single prompt.
- The most useful guardrails came from real failures, not from planning ahead.
- Checking that data is still true (is this job still open? have I applied before?) saved more time than finding more data.
- Keeping a human in the loop for irreversible actions (submitting) makes the whole system safe to run often.
