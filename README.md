# AI Personal Secretary

A reusable AI skill for honest end-of-day accountability. Tell it what you did; it compares your day with **your** goals and current strategy, gives one verdict, and keeps a plain Markdown record.

```text
Secretary, daily report: studied 3h, exercised 1h, worked on my website 2h, sent one client email.
```

The reply is short: **ON TRACK**, **MIXED**, or **DRIFTED**, a reason grounded in your report, a seven-day scoreboard, and one suggestion for tomorrow. Necessary obligations and deliberate rest are treated fairly.

## What's in this repo?

| File | Purpose |
| --- | --- |
| [`personal-secretary/SKILL.md`](personal-secretary/SKILL.md) | The installable core skill. |
| [`templates/`](templates/) | Starter files for your time zone, goals, and strategy. |
| [`PORTABLE.md`](PORTABLE.md) | Short instructions for AI assistants without skill-file support. |
| [`docs/original-starter-kit.md`](docs/original-starter-kit.md) | The complete supplied Claude Code starter kit, including optional calendar, inbox, deadline, and helper-agent ideas. |

The original kit calls the secretary **Gang** and uses `~/Desktop/gang/`. This repo's installable version uses the generic name **Personal Secretary** and `~/personal-secretary/` by default. The core works without the optional additions.

## Set up the core

1. Install the [skill file](personal-secretary/SKILL.md) in your AI tool using one of the paths below.
2. Create a `~/personal-secretary/` folder. Copy the three files in [`templates/`](templates/) into it as `config.md`, `goals.md`, and `strategy.md`.
3. Replace every `<placeholder>` with your own facts. In `config.md`, set an [IANA time zone](https://www.iana.org/time-zones) such as `Asia/Hong_Kong`. Keep your goals and strategy specific enough to judge a day fairly.
4. Start a fresh AI session and send a daily report. Include approximate hours and any numbers you want in the scoreboard.

Your personal files stay in the data folder you chose. Keep them private if they contain sensitive details.

### Claude Code

Copy the `personal-secretary` folder to `~/.claude/skills/personal-secretary/` for all your projects, or to `<your-project>/.claude/skills/personal-secretary/` for one project. Then address the assistant as `Secretary` or invoke `/personal-secretary`. See [Claude Code's skill guide](https://code.claude.com/docs/en/skills).

### Codex

Copy the `personal-secretary` folder to `~/.agents/skills/personal-secretary/` for personal use, or to `<your-project>/.agents/skills/personal-secretary/` for one repository. Address the assistant as `Secretary` or invoke `$personal-secretary`. See [Codex's skill guide](https://learn.chatgpt.com/docs/build-skills).

### ChatGPT or another AI

Copy the `text` block from [`PORTABLE.md`](PORTABLE.md) into your assistant's custom or project instructions. Provide your goals and strategy in that project. If the assistant cannot read and write local files, save its daily log responses yourself and paste recent history into new chats. For ChatGPT, see [custom instructions](https://help.openai.com/en/articles/8096356-custom-instructions-for-chatgpt) or [project instructions](https://help.openai.com/en/articles/10169521-projects-in-chatgpt).

## Daily use

| Say | What you get |
| --- | --- |
| `Secretary, daily report: ...` | Verdict, evidence, scoreboard, one nudge, and a log entry. |
| `Secretary, show me the last 7 days` | Recorded verdicts, totals, and one pattern. |
| `Secretary, weekly review` | Short review of the week's reports. |
| `Secretary, monthly review` | Progress, bottleneck, and one next-month objective. |
| `Secretary, update strategy: ...` | A dated strategy change while older logs remain intact. |

Missing days are missing data. When you did not give a number, the secretary records `unknown` rather than guessing.

## Optional additions

The [original starter kit](docs/original-starter-kit.md) explains how its author added a deadline table, calendar access, inbox watch, blind-spot questions, and specialist helper agents in Claude Code. Set up only the additions you want, using the tools and permissions available in your AI environment.
