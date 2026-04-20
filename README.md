# AI Chief of Staff

An AI Chief of Staff that runs your day. It checks your email, calendar, and task tracker, surfaces what matters, drafts responses, and keeps a living "State Dashboard" of your projects, people, and priorities — so nothing falls through the cracks.

Works with **Claude Code** and **Cowork** (Claude Desktop).

---

## What it does

- **Daily briefing** — what you missed, what's due today, who's waiting on you, what to do next.
- **Signal filtering** — skips newsletters and bot noise, surfaces real people and real deadlines.
- **State Dashboard** — one source of truth for projects, people, open loops, and watch items.
- **Phased rollout** — starts manual, adds desktop "watchers" when you're ready, moves to cloud routines once you trust it.
- **Snapshot-first safety** — nothing writes to the Dashboard without a rollback snapshot first.
- **Encrypted backup walkthrough** — sets up an end-to-end encrypted off-machine backup so your notes survive a dead laptop or a lost Notion account.

Picks your platform during setup: **Obsidian**, **Logseq**, a **plain markdown folder**, or **Notion**. All four are supported.

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

After installing, just say any of these in a session:

- "Run Chief of Staff"
- "Start my day"
- "What did I miss?"
- "Daily briefing"

The first time you use it, it walks you through a short setup: it asks about you, your projects, your priorities, and which note-taking platform you want to use. Then it's ready.

---

## Requirements

- **A note-taking platform** — one of Obsidian, Logseq, a plain markdown folder, or Notion. The skill picks for you based on a few questions.
- **Email + calendar** connected (Gmail / Google Calendar, or Outlook / Microsoft 365).
- **Optional**: a task tracker (Airtable, Notion, Linear, Asana), a messaging tool (Slack or Teams).

It works with whatever subset of tools you have connected — three sources done well beats eight done poorly.

---

## What's in this plugin

```
ai-chief-of-staff/
├── .claude-plugin/
│   ├── plugin.json           ← plugin manifest
│   └── marketplace.json      ← marketplace entry (for one-line install)
├── skills/
│   └── chief-of-staff/
│       ├── SKILL.md          ← main skill: briefings, setup, dispatch
│       └── references/       ← detailed playbooks
│           ├── signal-filters.md
│           ├── notion-conventions.md
│           ├── file-based-conventions.md
│           ├── platform-choice.md
│           ├── watcher-playbook.md
│           ├── rollout-reminder.md
│           └── backup-setup.md
└── README.md
```

---

## Version

**0.1.0** — first public release.

---

## License

MIT. Do what you want with it. If it's useful, a shout-out is appreciated but not required.

---

## Feedback / bugs

Open an Issue on this repo.
