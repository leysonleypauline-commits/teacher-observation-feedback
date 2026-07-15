---
name: meeting-minutes-from-transcript
description: Turns a raw meeting recording transcript into structured, Lark-ready meeting minutes with a Highlights section, a per-topic Discussion section, a Conclusion, and an Action Items table. Use this skill any time the user pastes or uploads a meeting transcript (or a recording's auto-generated transcript) and asks for minutes, meeting notes, a meeting summary, or wants it "written up" — even if they don't say "minutes" explicitly. Also trigger on requests like "turn this transcript into a doc" or "summarize this meeting for Lark." Do not use for live note-taking without a transcript, or for short 1:1 chat logs that aren't meeting recordings.
---

# Meeting Minutes from Transcript

Converts a meeting transcript into polished minutes, formatted so they can be pasted directly into a Lark Doc with no further cleanup.

## Output structure

Always produce these five parts, in this order:

```
# {MM-DD}| {Meeting Name}

**Date:** {Month D, YYYY}  **Duration:** {X min Ys}  **Attendees:** {Name 1, Name 2, ...}

## Highlights
- {condensed, quick-scan bullets — the 4-8 things someone would need to know if they only read this section}

## Discussion
### 1. {Topic} ({Owner/speaker who led this topic})
- {bullet}
- {bullet}
### 2. {Topic} ({Owner})
- {bullet}
...

## Conclusion
- {short wrap-up: overall outcome of the meeting, what was decided, what happens next, how/why it closed}

## Action Items

**{Owner Name}**
1. {task} — Due: {YYYY-MM-DD}
2. {task} — Due: {YYYY-MM-DD}

**{Owner Name}**
1. {task} — Due: {YYYY-MM-DD}
```

### Title line
Use `MM-DD| {Meeting Name}` (e.g. `07-02| ESL Weekly Meeting`). Pull the meeting name from the transcript/call metadata if present; otherwise infer a short descriptive name from the dominant subject matter (e.g. "ESL Weekly Meeting," "Q3 OKR Sync").

### Metadata line
One line, bold labels, in this exact order: **Date:**, **Duration:**, **Attendees:**. Pull attendees from the transcript's participant list or from who is quoted speaking. If duration isn't in the transcript, omit that field rather than guessing.

## How to build each section

### Highlights
Write this **last**, after Discussion and Conclusion are drafted — it's a condensed pass over everything, not a first impression. 4-8 short bullets. Each bullet is one decision, milestone, or number that a skimmer needs (e.g. "July QA reviews at ~13%, up from 8% baseline at this point in June"). No sub-bullets, no owner tags — just the facts, ranked by importance.

### Discussion
This is the detailed record and should look like the sample the user provided: numbered topics in the order they came up in the meeting, each headed by `### N. {Topic} ({Owner})` where Owner is whoever raised/led that agenda item. Under each topic, bullet every distinct point, decision, number, or concern raised — one bullet per idea, not one bullet per sentence. Preserve specific figures, names, and dates exactly as stated in the transcript. Attribute a point to a specific person only when the transcript makes that attribution clear (e.g. "Nate stressed..."); don't invent attribution.

Skip a topic heading only if the meeting genuinely had no distinct agenda items — never collapse multiple unrelated topics into one heading for brevity.

### Conclusion
3-6 bullets or a short paragraph — whichever fits the meeting better. Cover: the overall state of things coming out of the meeting, any explicit closing remarks (who closed the meeting and what they asked for going forward), and anything that spans multiple topics (e.g. a recurring theme across two agenda items). This is different from Highlights: Highlights is "what happened," Conclusion is "so what / what's next."

### Action Items
No table — group by owner instead, as a bolded owner name followed by a numbered list of that owner's tasks. This format pastes as plain, natively-editable Lark lists (no table-paste issues) and doubles as ready-to-copy input for creating individual Lark Tasks.

- One `**{Owner Name}**` heading per person who has at least one action item, in the order they were first raised.
- Under each owner, a numbered list — one line per task: `{task} — Due: {YYYY-MM-DD}`.
- If a task genuinely has two owners (e.g. "Fleta Tang, Nate" review the same document), either list it once under each owner's heading, or give it a shared two-name heading — pick whichever reads more naturally for that item.
- **Task text**: one concise sentence — what needs to happen. Fold in brief extra context if the transcript gives it (e.g. "before Emma's first class tomorrow").
- **Due date**: always a real calendar date (`YYYY-MM-DD`), resolved against the meeting date found in the transcript header/metadata. Never write "Not specified," "Ongoing," "This week," or similar placeholder text.
  - Relative phrases get resolved to an actual date: "today" / "by EOD" → the meeting date itself; "tomorrow" → meeting date + 1; "this week" → the Friday of the meeting's week (or the explicit day if one was named); "~1 week" → meeting date + 7.
  - A specific stated date/time (e.g. "July 3, 10:00 AM") is used as given, normalized to `YYYY-MM-DD` (add the time only if the transcript gave one and it's meaningfully precise, e.g. `2026-07-03 10:00 AM`).
  - Standing/continuous tasks with no natural end (e.g. "keep monitoring X's reports") still get a real date: default to **meeting date + 7 days** as a soft check-in/review target, not a hard deadline. If it's useful context that the task is recurring, fold a note like "(recurring)" into the task text rather than the date.
  - Any action item with genuinely no stated deadline also defaults to **meeting date + 7 days**.

If the same action item is mentioned more than once in the transcript, list it once.

## Formatting rules for Lark paste-readiness

- Use plain Markdown headers (`#`, `##`, `###`) — Lark Docs converts these to native heading blocks on paste.
- Use `-` for bullets in Highlights/Discussion/Conclusion — converts cleanly to native Lark bullet lists.
- No table for Action Items. Lark Docs does not reliably convert pasted Markdown pipe-table syntax into a native table, so pasted "tables" often land as locked, non-editable blocks. Use the owner-grouped bold-heading + numbered-list format instead — it always pastes as plain, editable text and doubles as ready-to-copy input for creating individual Lark Tasks per owner.
  - **Confirmed in production (2026-07):** pasting `**{Owner Name}**` followed by a numbered list of `{task} — Due: {YYYY-MM-DD}` lines causes Lark to auto-convert each line into a native checklist/task item — checkbox, assignee tag matched from the Lark account, and a reminder attached from the due date. This is now the validated, correct format; don't revert to a table.
  - Lark derives its own reminder *time* from the date automatically (e.g. "Next Wednesday 10:00 AM," "Today 3:30 PM") — the skill only needs to supply the calendar date, not a time, unless the transcript gave an explicit time.
  - A comma-separated owner heading (e.g. `**Fleta Tang, Nate**`) correctly tags both people as assignees on that task — safe to use for genuinely shared action items.
- Bold only the metadata labels (**Date:**, **Duration:**, **Attendees:**) and each owner's name heading in Action Items — nothing else in the body, to keep the doc visually clean.
- No emojis, no colored callouts, no nested bullet levels beyond one — keep it flat and clean like the reference file.
- Don't add a table of contents or extra front matter — the five sections above are the whole document.

## Handling messy or partial transcripts

- If speaker labels are unreliable (e.g. auto-generated as "Speaker 1"), do your best to map to real names using context (people referring to each other by name) and flag any you couldn't confidently resolve at the end of Discussion as a short note: `*Note: could not confirm identity of [Speaker X].*`
- If the transcript is cut off or missing a section (e.g. no closing remarks), write what's there — don't fabricate a Conclusion or invent action items that weren't discussed.
- If duration or attendee list isn't derivable from the transcript at all, omit that field from the metadata line entirely rather than writing "Not specified" there (unlike Action Items' Due column, where "Not specified" is expected).

## Example (condensed)

**Input:** transcript of a weekly ESL ops meeting covering QA review status, a new AI lesson-scoring initiative, new-teacher onboarding, and HR updates.

**Output:**

```
# 07-02| ESL Weekly Meeting

**Date:** July 2, 2026  **Duration:** 33 min 52s  **Attendees:** MJ Quan, Ley Pauline Leyson, Cheyenne Pillay, Nate, Nadine Panday, Andrea Huang, Maseeha Gani, Fleta Tang, Belinda Guo

## Highlights
- July QA reviews ~13% complete (vs. 8% baseline at this point in June).
- MJ asked Ley to start using Claude to score teacher lessons via the shared skill, starting today.
- Emma's onboarding is behind — two missed meetings — practical session run today ahead of her first class tomorrow.
- Nate and Fleta each demoed AI-assisted (Codex) report/assessment tooling as reference cases for the team's Lark automation roadmap.

## Discussion
### 1. QA & Lesson Report Review (Ley Pauline Leyson)
- All prior action items closed; July QA reviews ~13% done (June baseline was 8% at this point).
- Zach had a strong start to July: professional setup, no conduct issues, high-quality report, strong engagement.
- Aiden's lesson reports need continued monitoring.
- June engagement mixed: 54% of lessons showed room for improvement, 46% strong.

### 2. AI/Skill-Based Lesson Analysis (MJ Quan)
- MJ asked Ley to extend the meeting-minutes skill so it can also score teacher lessons.
- Process: install via shared GitHub link, validate output on a real recording, then update SKILL.md.
...

## Conclusion
- The team is standardizing on Claude/AI-assisted analysis (lesson scoring, meeting minutes, parent-facing reports) as the near-term direction, with Nate's and Fleta's demos serving as reference points.
- MJ closed the meeting asking that all future meeting minutes be generated using the shared skill, and for the team to flag any challenges as they adopt it.

## Action Items

**Ley Pauline Leyson**
1. Continue monitoring Aiden's lesson reports (recurring); regular check-ins on consistency/quality — Due: 2026-07-09
2. Install and start using the Claude skill to analyze lesson recordings and score teacher lessons; update SKILL.md once validated — Due: 2026-07-09

**Cheyenne Pillay**
1. Run Emma's practical session; remind her to record all calls; keep scheduling/communication in the group chat — Due: 2026-07-02
```

(Meeting date here is 2026-07-02: "today"/EOD tasks resolve to 2026-07-02, and the two items with no stated deadline default to meeting date + 7 = 2026-07-09.)
