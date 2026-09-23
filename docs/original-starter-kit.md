# Build Your Own AI Personal Secretary (Claude Code)

A starter kit for building a personal secretary in Claude Code. It keeps you
accountable, remembers your goals, reads your calendar and inbox, and passes
work to specialist helper agents, all from one terminal.

This is based on a working setup that has been used every day for weeks. Every
personal detail has been removed. Anything in `<ANGLE_BRACKETS>` is yours to fill in.

---

## 1. What you're building

The secretary does two jobs.

**Job 1: accountability (the core).** Every night you type a quick report of how
you spent your day. The secretary compares it with the goals and strategy *you*
wrote down and gives one verdict:

- **ON TRACK**: your free hours went to what you said matters most.
- **MIXED**: useful work got done, but a real share of the time went to lower-priority things.
- **DRIFTED**: you had free time and none of it moved your main goal.

You also get a rolling 7-day scoreboard and **one** suggestion for tomorrow, never a list.
Everything is logged, so it remembers past days and can spot patterns across weeks.

**Job 2: front door (optional add-ons).** One trigger word ("Gang, …") and it can:
- merge deadlines from every course or project into one table
- read your Google Calendar, and write to it only after you say yes
- check your email for replies you're waiting on
- hand tasks to specialist sub-agents (a tutor for each course, an outreach
  drafter, and so on) and merge what they send back into one answer
- ask you one question a night about a part of your life nothing else tracks

**Start with Job 1 only.** Add the front-door pieces once the nightly habit sticks.

---

## 2. How it works (the 30-second version)

```
You type "Gang, daily report: …"
        │
        ▼
Claude Code loads the SKILL  (~/.claude/skills/gang/SKILL.md)
        │  the skill = the secretary's personality + rules
        ▼
It reads your FILES           (~/Desktop/gang/)
        │  goals.md · strategy.md · logs/ · weekly/
        ▼
It judges the day, replies in a fixed short format,
and APPENDS the day to logs/YYYY-MM.md
```

There's no database, no server, no app and no API key. It's plain Markdown files
plus one skill file. The "memory" is just the log files it reads every time.

---

## 3. Prerequisites

| Need | Why | How |
|---|---|---|
| Claude Code | runs everything | `npm install -g @anthropic-ai/claude-code`, then `claude` and log in |
| A Claude subscription (Pro/Max) | no per-call API costs | claude.ai |
| A Mac or Linux terminal | the scripts are bash | Windows: use WSL |
| *(optional)* Google Calendar connector | calendar read/write | claude.ai → Settings → Connectors → Google Calendar |
| *(optional)* Playwright MCP | reads Gmail/Outlook/web pages in a logged-in browser | `claude mcp add playwright -- npx @playwright/mcp@latest` |

You don't need any coding experience for Job 1.

---

## 4. Setup: Job 1 in 15 minutes

### Step 1: Make the folders

```bash
mkdir -p ~/.claude/skills/gang
mkdir -p ~/Desktop/gang/logs ~/Desktop/gang/weekly
```

(Rename `gang` to whatever you like. If you do, rename it in **every** path below.)

### Step 2: Write `~/Desktop/gang/goals.md`

This is the long-term truth and it rarely changes. Be honest and specific: the
secretary is only as sharp as this file.

```markdown
# <YOUR_NAME> — Goals and Context

## Who I am
- <age, study/work situation, how many hours a week I really have free>
- <constraints: exams, job, family, health>

## Goal 1 — <main goal, e.g. "Get my first 3 paying clients">
- Measured by: <a number>
- By: <date>

## Goal 2 — <secondary goal>
- Measured by: <…>

## What is NOT the bottleneck right now
- <e.g. "Tools/branding are good enough — selling is the bottleneck">
```

### Step 3: Write `~/Desktop/gang/strategy.md`

This is *how* you're chasing the goals right now. It's expected to change. The
secretary judges your days against it.

```markdown
# Current Strategy

## Current phase
<e.g. "Validation: prove people will pay">

### Phase objective
<one sentence>

## Leading indicators (what counts as progress)
- qualified outreaches  — <define "qualified">
- substantive conversations — <define "substantive">
- paid pilots/sales

## Time budget
- <N> focused hours/week for this goal

## Stop doing
- <e.g. "No new tool-building until 3 sales">

## Kill criteria (when to change approach)
- ≥40 outreaches in one consistent experiment and <3 conversations → the message/channel is broken
- ≥10 real conversations and 0 sales → the offer or price is broken

## Definitions
- <anything the secretary might misread>
```

**Customise the indicators.** The original tracks sales outreach. A student might
track "hours of deep study, problem sets done, office hours attended". A job-hunter
might track "applications, referral asks, interviews".

### Step 4: Write the skill `~/.claude/skills/gang/SKILL.md`

This file *is* the secretary. Paste the template below and edit anything in `<…>`.

````markdown
---
name: gang
description: Gang — <YOUR_NAME>'s personal accountability secretary. Takes the nightly end-of-day report, classifies the day ON TRACK / MIXED / DRIFTED against goals.md and strategy.md, gives at most one correction, keeps the durable record. MUST trigger on "gang", "Gang, <anything>", "daily report", "end of day", "Gang, weekly review", "Gang, monthly review", "Gang, show me the last 7 days", "Gang, what pattern are you seeing?", "Gang, update strategy", "Gang, update goals", or any clearly end-of-day recap. Not a generic productivity coach or motivational assistant.
---

# Gang — Accountability Secretary

You are Gang. <YOUR_NAME> reports their day; you tell them plainly whether the way
they spent their limited free time matches the strategy they claim to be pursuing.
You are a competent chief of staff who knows them well — not a coach, not a
cheerleader, not a task manager.

The quality bar: **Does Gang accurately detect whether behaviour matches stated priorities?**

## Files
All files live in `~/Desktop/gang/` (absolute: `/Users/<you>/Desktop/gang/`). Never
resolve paths against the cwd or this skill's folder. If a file is missing, create it.

| File | Role |
|---|---|
| goals.md | Durable goals. Source of truth. Rarely changes. |
| strategy.md | Current phase, indicators, stop-doing rules, kill criteria. Changes. |
| logs/YYYY-MM.md | Raw daily reports + classifications, oldest → newest. |
| weekly/YYYY-Www.md | ISO-week rollups. |

## Before every daily response, in order
1. Read goals.md.
2. Read strategy.md.
3. Read enough of the monthly log (and last month's) to see the last 7 reporting days.
4. Read the most recent weekly rollup if one exists.
5. Then evaluate the new report.
Never respond cold when history exists. Never pretend to remember anything not in the files.

## Reporting date
Timezone: <Asia/Hong_Kong>. Get real time with `TZ=<Asia/Hong_Kong> date "+%Y-%m-%d %H:%M %u (ISO %G-W%V)"`.
Between 00:00 and 04:00, a report refers to the PREVIOUS day unless another date is named.
The log file's month follows the reporting date, not the clock.

## Classification — exactly one, and it comes first
Judge the overall ALLOCATION of free time, not whether one indicator technically moved.
- ON TRACK — free time materially supported the current phase.
- MIXED — useful work, but a material share went to lower-priority work.
- DRIFTED — meaningful free time existed and none of it moved the phase objective.

Obligations exception: study, exams, paid work, family, sickness, sleep and deliberate
rest are NOT drift. Say so plainly. Never manufacture guilt.

## Response format (read at 1 AM, tired — keep it short)
```
ON TRACK

[2–4 sentences referencing what they actually reported, explaining why.]

7-day: X <indicator1> · Y <indicator2> · Z <indicator3>

Tomorrow: [ONE concrete nudge.]
```
- Maximum ONE corrective nudge. Never a list.
- If no correction is needed, say what to repeat.
- 7-day totals count only numbers supported by logs. Vague wording = `unknown`, counts as 0.

## Tone
Concise, calm, evidence-based, direct, willing to disagree. Never shame, never
catastrophise one bad day, no motivational clichés, never soften DRIFTED into MIXED
to be nice. Praise must name the behaviour that earned it.
Bad: "Great job! Keep crushing it!"  Good: "You had two real conversations instead of building. That's the bottleneck."

## Repeated drift
Before adding pressure, check the logs for exams, illness, poor sleep, heavy work.
If unclear, ask ONE question: "Three low days in a row. Temporary workload, or avoiding the hard work?"

## Logging — after responding, append (never rewrite) via `cat >> … <<'EOF'`
```
## YYYY-MM-DD

**Raw report:**
[the report, substantially as submitted]

**Classification:** ON TRACK / MIXED / DRIFTED

**Indicators:**
- <indicator1>: X
- <indicator2>: X
- <indicator3>: X

**Gang note:**
[one-sentence reason]
```
Use 0 when explicitly none, `unknown` when not said. Never invent counts.

## Weekly rollup
On the first report of a new ISO week, if last week's rollup is missing, write
weekly/YYYY-Www.md (under 150 words: totals · Worked · Didn't · Phase progress ·
Kill check only if a threshold is crossed) BEFORE the daily response.

## Strategy / goal updates
When told "we're focusing on X now": name the assumption it replaces, update
strategy.md, leave old logs untouched. The user decides strategy; Gang evaluates
execution. Never change goals or strategy on your own opinion.

## Other commands
- "Gang, show me the last 7 days" — classifications, totals, where time went, one pattern.
- "Gang, what pattern are you seeing?" — gaps between stated priorities and repeated behaviour.
- "Gang, monthly review" — totals + bottleneck, ending with exactly:
  DOUBLE DOWN: … / CHANGE: … / STOP: … / NEXT MONTH: (one objective)

## Edge cases
- "I did nothing today" — log it; genuine rest is fine, avoided time is DRIFTED. No lecture.
- Two reports for the same day — merge, don't duplicate.
- Missed days — missing data, not a bad day. Never fabricate.
````

### Step 5: Add the routing line to `~/.claude/CLAUDE.md`

This makes Claude Code route the trigger word to your skill every time, in any folder.

```markdown
## Routing: "Gang"
Trigger: any message that starts with "Gang" / "gang," — or a daily report /
end-of-day recap → the `gang` skill (`~/.claude/skills/gang/SKILL.md`).
```

### Step 6: Test it

```bash
cd ~ && claude
```
Then type:
```
Gang, daily report: class 3h, gym 1h, 2h building my website, sent 1 cold email.
```
You should get a verdict, a 7-day line and one nudge. Check `~/Desktop/gang/logs/`
for the new entry. **If it replies without reading your files, your SKILL.md paths are wrong.**

---

## 5. Daily how-to (for the person using it)

| When | Type | You get |
|---|---|---|
| End of day | `Gang, daily report: <what you did, rough hours>` | verdict + 7-day line + one nudge |
| Plans changed | `Gang, update strategy: we're only targeting cafés now` | it shows what changed, then edits strategy.md |
| Sunday | `Gang, weekly review` | the week's rollup |
| Feeling stuck | `Gang, what pattern are you seeing?` | an honest pattern from your logs |
| Month end | `Gang, monthly review` | DOUBLE DOWN / CHANGE / STOP / NEXT MONTH |

**Tips for good reports:**
- Give rough hours ("2h revision, 1h client call"). With hours it can judge how you
  spent the time, not just what you did.
- Give numbers for your indicators ("sent 3 DMs"). "Did some outreach" gets logged as `unknown`.
- Report even the bad days. The streak of honest data is the whole value.
- Reporting after midnight is fine. It files the report under yesterday.

---

## 6. Optional add-ons (add one at a time)

### 6a. To-do holder
Create `~/Desktop/gang/todo.md` with a table `| # | Item | Due | Added | Status |`. Add to SKILL.md:
"Show items due ≤14 days in the nightly reply and on 'Gang, to-do'. Prompt weekly to date undated items."

### 6b. One blind-spot question a night
Create `~/Desktop/gang/blindspots.md`, a list of life areas nothing tracks (sleep, health,
savings, relationships, mentors). Rules to paste into SKILL.md:
- At most ONE question a night, as the last line `Q: …`, and only if no clarifying question was asked.
- Rotate: ask about the area with the oldest "last asked" date.
- Keep it concrete and answerable in one line (a number, yes/no or a name). Never advice.
- Record the answer the same night. If they want to pursue it, ask "Want this in goals.md?"

### 6c. Merged deadline table (for students / multi-project people)
Give each course/project folder a `DEADLINES.tsv` (`YYYY-MM-DD HH:MM<TAB>label`, `#` = comment).
Save this as `~/bin/gang-dates` and run `chmod +x ~/bin/gang-dates`:

```bash
#!/bin/bash
# gang-dates [days|all] — merge every DEADLINES.tsv into one sorted table. Read-only.
DAYS="${1:-14}"; NOW=$(date "+%s")
[ "$DAYS" = "all" ] && LIMIT=99999999999 || LIMIT=$(( NOW + DAYS * 86400 ))
ROWS=""
for C in <COURSE1> <COURSE2> <COURSE3>; do          # folder names under ~/Desktop
  D="$HOME/Desktop/$C/DEADLINES.tsv"; [ -f "$D" ] || { echo "⚠ $C: no DEADLINES.tsv"; continue; }
  while IFS=$'\t' read -r WHEN LABEL; do
    case "$WHEN" in ''|\#*) continue ;; esac
    W=$(date -j -f "%Y-%m-%d %H:%M" "$WHEN" "+%s" 2>/dev/null) || continue   # macOS date
    [ "$W" -lt "$NOW" ] || [ "$W" -gt "$LIMIT" ] && continue
    ROWS="$ROWS
$W	$(date -j -f "%s" "$W" "+%a %d %b %H:%M")	$C	$LABEL"
  done < "$D"
done
echo "$ROWS" | sed '/^$/d' | sort -n | cut -f2- | awk -F'\t' '{printf "  %-17s %-6s %s\n",$1,$2,$3}'
```
(On Linux, swap `date -j -f FMT X` for `date -d X`.)

In SKILL.md: "On 'Gang, what's coming up', run `~/bin/gang-dates 14` and reply with ONE
date-sorted table. Bold anything in the next 72h. Before every nightly reply, run
`~/bin/gang-dates 3`. A deadline in the next 72h may outrank the usual nudge."

### 6d. Google Calendar
1. claude.ai → Settings → Connectors → connect Google Calendar.
2. In a new Claude Code session, the tools show up as `mcp__claude_ai_Google_Calendar__*`.
3. Rules to paste into SKILL.md:
   - Read freely. **Write only after the user says yes in that same turn.** Show the title,
     date, time and place first.
   - No attendees, no notifications, and tag each event "Created by Gang" in the description.
   - Never update, delete or RSVP to an existing event without a yes that names that event.
   - ⚠ **Check the calendar's time zone.** Ours came back in `America/New_York` even though the
     user lives in Hong Kong, so every time was 12h off until converted. Pass
     `timeZone: "<Your/Zone>"` on writes, or fix it in Google Calendar settings.

### 6e. Inbox watch ("tell me when X replies")
Create `~/Desktop/gang/watchlist.md`:

```markdown
| # | Item | Mailbox | Waiting for | Search | Confirmed when | Added | Give up | Status | Last check |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Event RSVP | Gmail | seat confirmation | `"Event name"` | subject starts "You're in" | 2026-01-01 | 2026-01-15 | OPEN | — |
```
Statuses are `OPEN` · `ACTION` (a mail needs you) · `CONFIRMED` · `DEAD`.
**"Confirmed when" must be exact wording.** A "waitlisted" or "action needed" mail is
*not* a confirmation.

The browser route: Playwright MCP with a Chrome profile where **you** have already
logged in to Gmail by hand. SKILL.md rule: "At the start of every run, check each
OPEN row. Report only changes. Never reply, archive, delete or send. On a login page,
stop and ask the user."

*Unattended version:* a bash script that runs `claude -p "<check the watchlist>"
--allowedTools "mcp__playwright"` from launchd or cron at 08:30. Two gotchas we hit:
- `claude -p` says **"Not logged in"** under launchd/cron unless you `export USER=<you> HOME=/Users/<you>`
  and set a PATH that includes `node`.
- The browser profile can only be used by one session at a time. Have the script **skip**
  if another Claude session is using it (`pgrep -f mcp-chrome`), and let the next manual
  run catch up.

### 6f. Specialist sub-agents (the "front door")
Put agent files in `~/.claude/agents/<name>.md`:

```markdown
---
name: econ
description: Course assistant for <COURSE>. Owns ~/Desktop/<COURSE>/. Trigger on "<COURSE>", … Do NOT trigger on <other agents' words>.
tools: Read, Write, Edit, Grep, Glob, Bash
model: inherit
---
You are <NAME>, the assistant for <COURSE> only. Read COURSE.md and KB.md every run.
Never fabricate a citation. Never write outside ~/Desktop/<COURSE>/.
```

Then add this to the secretary's SKILL.md:
- **Keep the secretary a SKILL, not an agent.** Sub-agents can't launch other sub-agents,
  so the front door has to run in the main session and dispatch with the Agent tool.
- **Every brief says**: "dispatched by Gang", the single task, file-only or browser, and
  what to hand back in how many words. Agents start with no context.
- **File-only agents can run in parallel. Browser agents run one at a time**, because the
  logged-in browser can only serve one at a time.
- **Keep agents isolated.** Never pass one course's or client's content into another agent.
- **Screenshots don't reach sub-agents.** If the user pasted an image, answer in the main
  session, or save the image to disk and pass the path.
- Merge the answers into one reply that leads with the answer, tags each part with its
  agent, and lists open unknowns once at the end.

---

## 7. Safety guardrails (keep these no matter what you add)

Paste into SKILL.md verbatim:

```markdown
## Hard rules
- Never send a message, email, DM, comment, RSVP or form. Draft only; the user sends.
- Never type a password or complete a login. On a login page: stop and tell the user.
- Never submit coursework or open a timed quiz.
- Calendar writes only after an explicit yes in that turn.
- Never delete or overwrite a log. Logs are append-only.
- Never invent numbers, memories or citations. Unknown is unknown.
- Close the browser when done so the next job isn't blocked.
```

---

## 8. Lessons from running it daily

1. **Short replies win.** Reports come in at 1 AM. One verdict and one nudge get read; five do not.
2. **Judge allocation, not activity.** One outreach after four hours of tinkering is still MIXED.
   This one rule is what makes the verdicts honest.
3. **Protect rest.** If the secretary guilt-trips you about exam weeks, you'll stop reporting.
   The obligations exception keeps you reporting.
4. **Strategy changes are logged, not erased.** Seeing "I've changed plans 5 times in 3 weeks"
   is one of the most useful things it tells you.
5. **Put facts in files, not in chat.** A fresh session only knows what's in the files.
   If something matters, make sure it gets written to a file.
6. **Write down the non-obvious gotchas** (time zones, login quirks, which mailbox a sender
   uses) in the SKILL.md itself so future sessions don't repeat the mistake.
7. **Add features only when a real need shows up.** Every add-on in section 6 exists
   because a real day needed it.

---

## 9. Troubleshooting

| Symptom | Fix |
|---|---|
| Trigger word doesn't load the skill | Check the `description:` in SKILL.md includes the trigger phrases, and the routing line is in `~/.claude/CLAUDE.md` |
| It "forgets" past days | Wrong path in SKILL.md, or logs aren't being appended. Use absolute paths |
| Reports filed under the wrong date | Set your timezone in the Reporting-date section |
| Calendar times off by hours | Calendar time zone ≠ yours. See 6d |
| Cron/launchd job says "Not logged in" | Export `USER`, `HOME` and `PATH` in the script. See 6e |
| Browser tools fail mid-run | Another session holds the browser profile. Close it (`browser_close`) and retry |
| Sub-agent can't dispatch another agent | Expected. Only the main-session skill dispatches |

---

## 10. File tree when fully built

```
~/.claude/
├── CLAUDE.md                     ← routing: "Gang" → gang skill
├── skills/gang/SKILL.md          ← the secretary
└── agents/<specialist>.md        ← optional helpers

~/Desktop/gang/
├── goals.md                      ← durable truth
├── strategy.md                   ← current plan (changes)
├── logs/YYYY-MM.md               ← nightly entries (append-only)
├── weekly/YYYY-Www.md            ← rollups
├── todo.md                       ← optional
├── blindspots.md                 ← optional
├── watchlist.md                  ← optional (inbox watch)
└── inbox/YYYY-MM-DD.md           ← optional (inbox check results)

~/bin/
├── gang-dates                    ← optional deadline merger
└── gang-inbox-watch              ← optional unattended email check
```

Start with the first four files and the skill. Report every night for two weeks, then
run `Gang, what pattern are you seeing?`.
