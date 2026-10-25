# Agent Guide: job-apply-agent

PUBLIC repo. Source of truth for the `job-apply` skill. Hermes reads the skill from `~/.hermes/skills/job-apply`, a symlink into this repo. Anything personal lives in gitignored paths or env vars, never in tracked files.

## Layout

```
skills/job-apply/SKILL.md            the skill Hermes runs (edit here, not under ~/.hermes)
skills/job-apply/reference/          generic notes: ats-notes.md, tracker.md (tracked)
skills/job-apply/reference/*-private.md  employer-specific notes (gitignored)
assets/profile.example.yaml          tracked template
assets/profile.yaml                  PRIVATE: your details and reusable answers
assets/upload/                       PRIVATE: resume.pdf, profile.jpg
assets/leak-extra.txt                PRIVATE: extra strings bin/leak-check must never find (one per line)
applications/                        PRIVATE except README.md: log.md, events.md, jd/
bin/leak-check                       fails if a private value is in tracked files or history
```

## How it runs
- Browser: Playwright MCP driving a shared streamed Chromium. `assets/upload/` is mounted read-only into the MCP container as `/home/node/files`; uploads must use those paths.
- Repo root: env `JOB_APPLY_DIR`, else the repo the skill symlink points into. Browser URL: env `BROWSER_URL`, else `browser_url` in `profile.yaml`.
- Any `profile.yaml` value written `env:NAME` is read from environment variable `NAME` (`printenv NAME`). Use it for values you do not want on disk.

## Keeping it public-safe
- Never commit: `assets/*` (except README and example), `applications/*` (except README), `*-private.md`, `.env`, PDFs, images, or anything with real names, emails, phone, salary, private URLs, host paths, or employers you applied to. `.gitignore` is the safety net, not the plan.
- Tracked files use placeholders (`you@example.com`, `<repo>`). Employer-specific lessons go in `reference/*-private.md`; generic ATS lessons in `ats-notes.md`.
- Run `bin/leak-check` before every commit that goes out; it scans tracked files and history for values taken from `assets/profile.yaml` and `assets/leak-extra.txt`. Install as a hook once: `ln -s ../../bin/leak-check .git/hooks/pre-push`. `bin/leak-check --selftest` proves it still catches leaks.
- Commit as a noreply identity, not a personal email. Push `main` only; do not push other local branches.

## Rules
- Agents stop before Submit; the user submits. Agents may attempt a CAPTCHA (max 2 retries), then stop and ask the user.
- Do not invent answers; unknown or sensitive fields are asked, not guessed. Current salary comes from `profile.yaml` only until `current_salary_expires` (6 months after approval); then ask again and refresh it.
- Only the user's word moves an application to `submitted` or later (see `skills/job-apply/reference/tracker.md`).
- Edit skills here, then `git commit`. Keep `SKILL.md` short; put site-specific detail in `reference/`.
- New skill = new directory under `skills/`, then symlink it: `ln -s "$PWD/skills/<name>" ~/.hermes/skills/<name>`.
- The upstream plugin we borrowed ideas from is a sibling clone `../job-apply-plugin` (MIT, written for Claude in Chrome); reference only, don't edit.
