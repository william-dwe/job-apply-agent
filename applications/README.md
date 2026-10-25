# applications/ (private, gitignored)

Tracker for job applications. Format and rules: `skills/job-apply/reference/tracker.md`.
- `log.md`: one summary row per application. The skill checks it before applying so the same job is not applied to twice.
- `events.md`: append-only history; `log.md` status equals the latest event.
- `jd/<id>.md`: saved posting text.
