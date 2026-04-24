# AI Chief of Staff

An AI Chief of Staff that runs your day. It checks your email, calendar, and task tracker, surfaces what matters, drafts responses, and keeps a living "State Dashboard" of your projects, people, and priorities — so nothing falls through the cracks.

Works with **Claude Code** and **Cowork** (Claude Desktop).

---

## What it does

- **Daily briefing** — what you missed, what's due today, who's waiting on you, what to do next.
- **Log** — a quick documentation router. Say "log this: …" or "captain's log: …" and it figures out where the content belongs (task update, project note, State Dashboard, or Captain's Log) and writes it there. Works on its own OR through the Chief of Staff.
- **Signal filtering** — skips newsletters and bot noise, surfaces real people and real deadlines.
- **State Dashboard** — one source of truth for projects, people, open loops, and watch items.
- **Phased rollout** — starts manual, adds desktop "watchers" when you're ready, moves to cloud routines once you trust it.
- **Snapshot-first safety** — nothing writes to the Dashboard without a rollback snapshot first.
- **Encrypted backup walkthrough** — sets up an end-to-end encrypted off-machine backup so your notes survive a dead laptop or a lost Notion account.

Picks your platform during setup: **Obsidian**, **Logseq**, a **plain markdown folder**, or **Notion**. All four are supported.

---

## Two skills, one system

This plugin gives you **two skills** that share the same data:

| Skill | What it does | How to invoke |
|-------|-------------|---------------|
| **Chief of Staff** | Full briefings, signal filtering, task dispatch, State Dashboard | "Run Chief of Staff", "daily briefing", "what did I miss?" |
| **Log** | Quick documentation router — parses what you said, routes to the right place (task, project, dashboard, or Captain's Log), writes it there | "Log this: ...", "captain's log: …", "document this", "remember this" |

**They work together but don't depend on each other.** You can use Log without ever setting up the Chief of Staff — it creates a Captain's Log on first use. And when you do run the Chief of Staff, it automatically picks up anything Log wrote to operational records or the Captain's Log.

Think of it this way: Log is for capturing things in the moment. Chief of Staff is for making sense of everything later.

---

## Install

### Option 1 — One-line install (recommended)

In Claude Code or Cowork, run:

```
/plugin marketplace add avigdor-star/ai-chief-of-staff
/plugin install ai-chief-of-staff
```

### Option 2 — Manual install

1. Clone this repo.
2. Copy the folder into your Claude Code / Cowork plugins directory.
3. Restart Claude Code / Cowork.

---

## How to use it

### Chief of Staff

After installing, just say any of these in a session:

- "Run Chief of Staff"
- "Start my day"
- "What did I miss?"
- "Daily briefing"

The first time you use it, it walks you through a short setup: it asks about you, your projects, your priorities, and which note-taking platform you want to use. Then it's ready.

### Log

Say any of these at any time — no setup needed:

- "Log this: shipped the new landing page"
- "Captain's log: decided to switch to Stripe billing"
- "Document this: the API rate limit is 100/min"
- "Remember this: the offer closes Friday"

The skill parses what you said, searches your tasks and State Dashboard for related items, and routes the content to the right place. If it's a task update, it updates the task. If it's reflective or personal, it goes in the Captain's Log (Win, Decision, Learning, etc. — type and project inferred automatically). If it's unclear, it asks. First time? It sets up a Captain's Log on the spot.

---

## Requirements

- **A note-taking platform** — one of Obsidian, Logseq, a plain markdown folder, or Notion. The skill picks for you based on a few questions.
- **Email + calendar** connected (Gmail / Google Calendar, or Outlook / Microsoft 365) — for the Chief of Staff briefings.
- **Optional**: a task tracker (Airtable, Notion, Linear, Asana), a messaging tool (Slack or Teams).

Captain's Log only needs a note-taking platform. The full Chief of Staff benefits from email and calendar connections, but works with whatever subset of tools you have connected.

---

## What's in this plugin

```
ai-chief-of-staff/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── skills/
│   ├── chief-of-staff/
│   │   ├── SKILL.md            ← full CoS: briefings, setup, dispatch
│   │   └── references/
│   │       ├── captains-log.md ← shared Captain's Log format (both skills use this)
│   │       ├── signal-filters.md
│   │       ├── notion-conventions.md
│   │       ├── file-based-conventions.md
│   │       ├── platform-choice.md
│   │       ├── watcher-playbook.md
│   │       ├── rollout-reminder.md
│   │       └── backup-setup.md
│   └── log/
│       └── SKILL.md            ← standalone quick documentation router
└── README.md
```

---

## Version

**0.3.1** — replaced standalone Captain's Log skill with the **Log** skill, a lightweight documentation router that picks the right destination (task, project, dashboard, or Captain's Log) instead of always writing to the Log. The Captain's Log database itself is unchanged.

---

## License

MIT. Do what you want with it. If it's useful, a shout-out is appreciated but not required.

---

## Feedback / bugs

Open an Issue on this repo.
