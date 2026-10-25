---
name: job-apply
description: "Fill out a job application from a posting URL (LinkedIn or company site) using the shared streamed Chromium and Playwright MCP tools. Stops before Submit; the user submits."
version: 0.2.0
metadata:
  hermes:
    tags: [Jobs, Applications, Browser, Playwright]
---

# Job apply

Input: one job posting URL. Output: the application form filled in the shared browser, ready for the user to review
and submit. **You never click Submit.** You may attempt a CAPTCHA yourself (max 2 retries after the first try); if it still fails, stop and ask the user.

## Hard rules
- Use ONLY the `mcp__playwright__*` tools. Do not use `browser_exec*` (the built-in browser needs a local Chrome and fails here).
- Never submit, send, or confirm the final step. Stop at the review screen and report.
- Never log in, create accounts, or enter passwords or 2FA codes. If a login wall appears, stop and tell the user to log in at the shared browser (`browser_url` in `profile.yaml`, or env `BROWSER_URL`).
- Never invent an answer. Anything not in `profile.yaml` or the CV ("have you worked here", sponsorship, consent, an expired current salary) is left blank and reported as a question for the user, unless the user already answered it in this chat.
- Current salary: use `current_salary_monthly_take_home_idr` from `profile.yaml` only while today is before `current_salary_expires` (6 months from when the user approved it). Once expired, ask the user, then update both values (new expiry = today + 6 months). Never use it for other questions (e.g. total compensation or bonus), and never ask the user to say it in a public place.
- Links come from `profile.yaml` `links` (linkedin, website, github). Use github only when a field asks for it.
- Do not reload the form page once fields are filled.

## Files (read via the file tools; paths are the same inside the Hermes container)
- Repo root: env `JOB_APPLY_DIR` if set, else the repo this skill directory is symlinked from (resolve with `realpath`). Paths below are relative to it.
- Profile and reusable answers: `assets/profile.yaml`. A value written `env:NAME` is read from environment variable `NAME` (terminal `printenv NAME`); never echo it beyond what a form field needs.
- Tracker (check first, update after), all in `applications/`: `log.md` (one row per application), `events.md` (append-only history), `jd/<id>.md` (saved posting). Format and rules: `reference/tracker.md`.
- Uploads (CV, photo): pass ONLY `/home/node/files/resume.pdf` and `/home/node/files/profile.jpg` to `browser_file_upload`. Host paths are rejected.

## Steps
1. Read `applications/log.md`. If the URL or company+role is already there, tell the user and stop.
2. Open the posting. Report title, company, location, on-site/remote, Easy Apply vs external, any visa/relocation requirement.
   If it needs US work authorization or relocation the profile doesn't cover, ask before continuing.
3. Click Apply. For an external site, follow the redirect (it may open a new tab: use `browser_tabs`). Note the final URL and ATS type.
4. List every field with required/optional. Fill what `profile.yaml` answers. For dropdowns that are custom menus (not `<select>`), read the options first and ask the user if the choice is a judgement call.
5. Upload the CV (and photo if required) with the `/home/node/files/...` paths. Click the upload button first so the file chooser is open, then call `browser_file_upload`.
6. Read the page back and report a field-by-field table, validation errors, and which fields you left empty and why. If a CAPTCHA is present, attempt it (max 2 retries); on failure stop and ask the user to solve it. Remind the user about the Submit button.
7. Save the posting text to `applications/jd/<id>.md`. Add a `log.md` row (status `form filled`; fill only what you observed: company, role, location, mode, source, url, ats, expected_salary, cv_version) and a `form filled` line in `events.md`. Set `submitted` and later states only when the user says so (see `reference/tracker.md`).

## Tracking
When the user reports news (submitted, screening, interview, offer, rejected, withdrawn), update `log.md` and append to `events.md` per `reference/tracker.md`. Requests like "tracker", "what's pending" or "stats" are answered from those files.

## Known quirks
See `reference/ats-notes.md`, plus any `reference/*-private.md` (gitignored) if present.
