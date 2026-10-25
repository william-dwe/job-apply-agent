# assets/ (private, gitignored)

Everything the agent needs to fill in a form, except the skills themselves. Nothing here is committed.

| File | What |
|---|---|
| `upload/resume.pdf` | CV attached to applications |
| `upload/profile.jpg` | Photo for forms that require one (keep under 5 MB) |
| `profile.yaml` | Your contact details and reusable answers (copy `profile.example.yaml`) |

`upload/` (only) is mounted read-only into the `playwright-mcp` container at `/home/node/files`
(see your browser stack's compose file). That is the ONLY place the browser tool can upload from, so a skill
attaches files with `/home/node/files/resume.pdf`, not a host path.
