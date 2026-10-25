# Application tracker

All files private and gitignored, under `applications/`. Design basis: research briefing 2026-10-25 (stage lists of 6-9 values, event history, max 2 follow-ups, ghost around 3 weeks).

## Files
- `log.md`: one summary row per application. Columns, in order:
  `id | company | role | location | mode | source | url | ats | expected_salary | cv_version | applied_on | status | last_event_on | next_action | next_on | followups | contact`
  - `id` = `company-role-YYYYMMDD` (lowercase slug, date = day the row was created).
  - `mode` = onsite | hybrid | remote. `source` = LinkedIn | company site | referral | recruiter.
  - `followups` = number of nudges sent (0-2). Blank means unknown, not 0, unless the row is `submitted` by us.
- `events.md`: append-only history, `| date | id | event | detail |`. Never edit or delete old lines; add a correction line.
  Events: form filled, submitted, followup sent, screening, interview (detail = round/stage + date), thank-you sent, offer, accepted, rejected, withdrawn, ghosted, note.
- `jd/<id>.md`: posting text saved at apply time (postings disappear).

## Status values (log.md `status`)
form filled, submitted, screening, interview, offer, accepted, closed.
`closed` carries its reason in the last event: rejected | withdrawn | ghosted | no response.
Status in `log.md` always equals the latest event. Update both in the same step.

## Rules
- Only the user's word moves a row to `submitted` or beyond. Agent never infers status from the page.
- Unknown or unobservable fields stay blank. No excitement score, no invented rejection reason.
- On `submitted`: set `applied_on`, `next_action` = follow up, `next_on` = applied_on + 14 days.
- Max 2 follow-ups (the second one week after the first). After the second with no reply, ask the user whether to close.
- Ghosted: no reply 21 days after `last_event_on` and followups used. Ask the user first; then log `ghosted` and set `closed`.
- After an `interview` event, remind the user to send a thank-you within 24 hours.

## "tracker" / "what's pending" request
Read `log.md`, list rows not `closed`/`accepted`, sorted by `next_on`; mark overdue rows (next_on before today) and ghost candidates. Read only; ask before changing anything.

## "stats" request
Count from `events.md` and say which definition you used:
- submitted = rows with a `submitted` event.
- response rate = submitted rows with any screening/interview/offer/rejected event / submitted. Rejections count as responses; say so.
- interview rate = rows with an `interview` event / submitted.
- offer rate = rows with an `offer` event / rows with an `interview` event.
- by source: same rates grouped by `source`. Time to first response = first non-submitted event date minus `applied_on`.
Small numbers: report counts next to percentages.
