# ATS and site notes

Add what each run teaches. Keep it factual (what the form did), no personal data.

## LinkedIn
- Logged-out view shows a sign-in wall; the shared browser must be logged in by the user.
- "Apply" (not "Easy Apply") = external. The link goes through `linkedin.com/safety/go` to the company site and often opens a new tab.

## Playwright MCP
- `browser_file_upload` only accepts paths under `/home/node` inside the playwright-mcp container (mounted from `assets/upload/`).
- The upload input is hidden behind a button: click the button to open the file chooser, then upload.
