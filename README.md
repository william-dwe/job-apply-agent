# job-apply-agent

Give the agent a job posting URL; it opens it, follows the Apply link, fills the form from your profile and CV in a
shared browser you can watch, and stops so you can review. It clicks Submit only if you tell it to.

## Pieces
- **Skill** `skills/job-apply`: instructions for Hermes. Symlink it into `~/.hermes/skills/job-apply`.
- **Browser**: any streamed Chromium + Playwright MCP exposing `mcp__playwright__*` tools, with `assets/upload/` mounted read-only at `/home/node/files`. Its URL goes in `browser_url` (profile.yaml) or env `BROWSER_URL`.
- **Assets** (private, gitignored): `assets/profile.yaml`, `assets/upload/resume.pdf`, `assets/upload/profile.jpg`.
- **Tracker** (private, gitignored): `applications/log.md`, `events.md`, `jd/`. Format: `skills/job-apply/reference/tracker.md`.

## Setup
1. `cp assets/profile.example.yaml assets/profile.yaml` and fill it in.
2. Put your CV and (if forms want it) photo in `assets/upload/`.
3. Log in to LinkedIn once in the streamed browser (it keeps the session).
4. Symlink the skill (already done on this host):
   `ln -s $PWD/skills/job-apply ~/.hermes/skills/job-apply`

## Use
Tell Hermes: "apply to https://www.linkedin.com/jobs/view/<id>/". It reports the job, fills the form, and lists what it
could not answer. It attempts any CAPTCHA (max 2 retries, then asks you). You check the form at the browser URL and submit, or tell the agent to. It fills mandatory fields only.

## Status
v0.1. Verified on LinkedIn to an external custom form (a company's custom form). Greenhouse, Lever, Workday and LinkedIn Easy Apply are untested here.
