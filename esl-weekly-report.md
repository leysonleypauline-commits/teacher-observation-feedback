---
name: esl-weekly-report
description: Generate the ESL QA weekly report by pulling the last 7 days of lesson reviews from the "T03-2 Teacher Review (ESL)1" and "ESL Reviews" tables in the ESL QA Lark Base, then producing a Lark-Doc-ready plain text report (lesson report quality themes + a student talk time write-up and score leaderboard), matching the style of the monthly "ESL QA" report. Use this whenever the user asks for the ESL weekly report, ESL QA report, weekly teacher review summary, wants this week's lesson/QA reviews summarized, or asks "what did QA find this week" for the ESL team — even if they don't say "skill" or name the Base directly.
---

# ESL Weekly QA Report

Produces a text report summarizing the past 7 days of ESL lesson reviews, formatted to paste
straight into a Lark Doc. It replaces the monthly "ESL QA" Looker-style report the user
originally showed as a PDF, scoped to a rolling weekly window and built only from the two
Base tables the user confirmed are needed.

## Data source

Fixed Lark Base (do not ask the user for this — it's already confirmed):

- `base_token`: `TN6gbENOya9ryiszcV2lD2Bjgoc`
- Table 1 — **T03-2 Teacher Review (ESL)1** (`table_id`: `tbl5utIgmygR0hV0`): one row per lesson
  review, with the real class date. This is the source of truth for "which reviews happened
  in the last 7 days."
- Table 2 — **ESL Reviews** (`table_id`: `tbltdUK3jtkgrB65`): one row per lesson review with
  clean categorical tags (STT Status, Lesson report quality, Improvement Needed, Learner
  Engagement). It has no date field — only a `Month` select — so it can't be filtered by
  week directly.

Both tables get a row per review at the same time, so a row in table 1 always has a matching
row in table 2 for the same lesson. Join them on **Teacher Lark Account (open_id) + Total
Score** — in practice a teacher rarely has two reviews with the identical score in the same
week, and this has been verified against real data in this Base to line up correctly. If a
teacher has multiple reviews this week with the same score, match them in the same relative
order they appear in each table (both tables are populated in review order).

Use `lark-cli base +record-list --as user` for both tables — no need for `+data-query`, the
weekly volume is small enough to reason over directly.

## Step 1 — Work out the date window

Use the current date available in context. The window is `[today - 7 days, today]` inclusive.
Because the Base's date filter only supports `is` / `isGreater` / `isLess` (no "greater or
equal"), filter with `isGreater` on the day *before* the window start:

```
lark-cli base +record-list --base-token "TN6gbENOya9ryiszcV2lD2Bjgoc" --table-id "tbl5utIgmygR0hV0" --as user \
  --filter-json '{"logic":"and","conditions":[["Date and Time of Class","isGreater","ExactDate(<window_start_minus_1>)"]]}' \
  --field-id "Teacher Lark Account" --field-id "Date and Time of Class" --field-id "Total Score" \
  --field-id "STATUS" --field-id "Comment from QA" --field-id "Detailed Comments from QA" \
  --limit 100
```

(e.g. for a window of 2026-07-10 to 2026-07-17, use `ExactDate(2026-07-09)`.) If the result's
`has_more` is `true`, page with `--offset` until you have everything — don't silently report
on a partial week.

## Step 2 — Pull the matching categorical tags

Work out which `Month` option(s) the window touches (a window can span a month boundary —
include both). Query table 2 filtered to those months:

```
lark-cli base +record-list --base-token "TN6gbENOya9ryiszcV2lD2Bjgoc" --table-id "tbltdUK3jtkgrB65" --as user \
  --filter-json '{"logic":"and","conditions":[["Month","intersects",["July 2026"]]]}' \
  --limit 200
```

Then, in your own reasoning (not a script), join each table-1 row to its table-2 row by
teacher `open_id` + `Total Score`, and keep only the table-2 rows that matched a review inside
the window. Rows in table 2 for the right month but outside the week (a different week of the
same month) won't have a matching table-1 row in this window — discard those.

## Step 3 — Synthesize the report

Don't just dump fields — read `Comment from QA` / `Detailed Comments from QA` alongside the
`Improvement Needed`, `Lesson report quality`, `STT Status`, and `Learner Engagement` tags, and
write natural, specific bullets the way a QA lead would, the same way the original monthly
report described individual teachers' patterns (e.g. "Kaleigh's reports occasionally lack
learner feedback and homework details"). Ground every claim in the data you pulled — don't
invent teachers, scores, or patterns that aren't in the rows you fetched.

If a teacher had more than one review this week, cover them once, synthesizing across their
reviews rather than repeating a bullet per lesson.

If zero reviews fall in the window, say that plainly instead of forcing the template.

## Report template

Use this structure, adapting section content to what the data actually shows (omit a bullet
group entirely if it has nothing to say — e.g. don't write an empty "flagged for follow-up"
section):

```
ESL QA Weekly Report — <window_start> to <window_end>

<N> lesson review(s) completed this week, covering <M> teacher(s).

Overall lesson report quality:
• <Teacher> — <specific note synthesized from Improvement Needed / Lesson report quality / QA comments>
• <Teacher> — <...>
(If every reviewed teacher was "Excellent" with no Improvement Needed tags, replace the bullets
with one line: "All lesson reports reviewed this week met or exceeded expectations.")

Student talk time
<One or two sentences naming teachers tagged "High STT" this week, and separately naming any
teachers tagged "Low STT" who may need coaching on handing more talk time to students.>

Score: <highest Total Score>
• <Teacher>
Score: <next score down>
• <Teacher>, <Teacher>
...continue down through every score present this week, one group per distinct score, teachers
sorted alphabetically within a group.

Flagged for follow-up: <only include this section if any review this week has STATUS = RED, or
Lesson report quality = "Needs Support" — name the teacher and why>
```

This intentionally does not include a "% of reviews complete" or "no lesson report submitted
by X" line — those require teacher-roster and submission-tracking tables outside this skill's
two-table scope. If the user wants those restored, they'll need to name the table(s) that track
overall teacher rosters and lesson-report submission status.

## Output

Return the finished report as plain text ready to paste into a Lark Doc — no markdown tables,
no code fences, just headings and bullets as shown above so pasting preserves formatting
cleanly in Lark Docs.
