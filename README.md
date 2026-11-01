# AI Job Search Agent

An agent workflow that uses **Claude Code** and the **Playwright MCP** to search company careers pages, filter roles against eligibility rules, score them against a resume, and prepare applications for human review.

I built this during my own graduate job search to replace hours of manual searching with a repeatable weekly run. The agent finds, ranks and pre-fills. A human always reviews and clicks Submit.

---

## What it does

| Phase | What happens | Output |
|---|---|---|
| 1. Search | Checks 50+ company careers pages. Reads public job feeds (Greenhouse, Lever, Ashby) where available instead of clicking through pages, and falls back to the browser for JavaScript-heavy sites. | Raw list of roles |
| 2. Filter and score | Applies skip rules (seniority, work-rights wording, location, role type, date window) and scores each remaining role out of 10 against the resume, with a one-line reason per score. | `jobs.csv`, `search_log.md` |
| 3. Prepare | For a role the user picks, opens the form in the user's own Chrome, fills standard fields from a profile file, re-checks every field, then stops. | A filled form, ready for review |
| 4. Track | After the user confirms submission, logs it. | `applied.csv` |

On a typical run it checks around 50 companies and produces a ranked shortlist plus a log of why every other role was skipped.

---

## How it works

```
task.md (rules, phases, scoring)  ─┐
profile.md (candidate details)    ─┼─► Claude Code ─► Playwright MCP ─► user's Chrome
resume.pdf                        ─┘        │
                                            ├─► jobs.csv        (ranked shortlist)
                                            ├─► search_log.md   (coverage per company)
                                            └─► applied.csv     (tracking)
```

- **Instructions as a spec.** All behaviour lives in `task.md`: phases, skip rules, a scoring rubric, standard form answers and stop conditions. Changing the search means editing a text file, not code.
- **Public job feeds first.** Many companies use an applicant tracking system with a public JSON feed. Reading the feed is faster and more reliable than navigating pages.
- **Runs in the user's real browser.** Playwright MCP's extension mode connects to an existing Chrome profile, so pages behave as they would for a normal visitor.

---

## Guardrails (and why each one exists)

Each rule came from something that went wrong in testing.

| Guardrail | What happened without it |
|---|---|
| Never click Submit; stop on the final page | Submission is a one-way action. A human should always review first. |
| Re-check every field after a resume upload | A resume parser auto-filled the GitHub field with a portfolio URL. |
| Stop if a field changes that the agent didn't change | The user and the agent edited the same form at once and fields were overwritten. |
| Fill fields first, then ask the user to upload files | The browser extension is not allowed to attach local files, so uploads are done by the user. |
| Never use the automated browser on job boards (SEEK, LinkedIn) | Automated activity on personal job-board accounts risks the account being flagged. |
| Stop at any CAPTCHA or bot check | Some systems reject automated submissions. The agent should never try to get around them. |
| Always ask the user about visa or sponsorship questions | These answers are personal and situational, so they should never be automated. |
| Never invent experience, skills or dates | Anything not in the profile or resume is left blank and flagged. |

---

## Files

| File | Public? | Purpose |
|---|---|---|
| `README.md` | Yes | This file |
| `task.template.md` | Yes | The agent spec with placeholders. Copy to `task.md` and fill in. |
| `profile.example.md` | Yes | Example candidate profile with fake details. Copy to `profile.md`. |
| `examples/jobs.sample.csv` | Yes | Example output format |
| `task.md`, `profile.md`, `applied.csv`, `*.pdf` | **No** | Real personal files. Excluded by `.gitignore`. |

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

---

## Limitations

- Some applicant tracking systems (for example, certain Lever and Workday configurations) reject submissions from automated browsers. The workflow is designed for the user to submit manually anyway.
- Careers pages change often. A company that can't be read in about two minutes is logged as skipped rather than retried.
- Scores are a starting point for human judgement, not a decision.

---

## What I learned

- Writing the agent's behaviour as a clear spec, with explicit stop conditions, mattered more than any single prompt.
- The most useful guardrails came from real failures, not from planning ahead.
- Keeping a human in the loop for irreversible actions (submitting) makes the whole system safe to run often.
