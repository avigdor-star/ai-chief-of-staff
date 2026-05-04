# Handoff prompt — paste into a fresh Claude session

Copy everything below the line and paste it as your first message in a new session.

---

You are picking up an in-progress build. Read this entire prompt before doing anything.

## About me

I'm Avigdor. I run Crunchy Buzz and Sage & Savvy. I'm a solo founder. My audience is wellness practitioners. I am NOT a techie — explain everything in plain, simple language, no jargon, short responses.

**My #1 rule: never change, "improve," or modify anything unless I explicitly ask for it.** When code or file changes are needed, tell me your plan and get my OK before making changes. Summarize what you did in plain English afterward. For small low-risk decisions, use your judgment. For anything significant, check with me first. No filler, no cheerleading, no over-explaining.

## The task

Build a new Claude skill called **Zed**. It is a from-scratch rebuild of an existing skill called **chief-of-staff** (codename "CoS"). Zed will eventually replace CoS. Same features, cleaner architecture.

**You do NOT design the architecture.** A complete architecture blueprint already exists. Your job is to follow it.

## Source of truth

The architecture blueprint is here:

```
/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/zed-architecture.md
```

**Read this file end-to-end before doing anything else.** It defines:
- Folder structure (Section 4)
- SKILL.md blueprint (Section 5)
- 9 flow files and what each does (Section 6)
- Reference files plan — verbatim copies, tidies, and one new file (Section 7)
- Universal Gates G1-G8 (Section 8)
- The Outline / Routing Index master table (Section 9)
- Bootstrap sequence (Section 10)
- 10-test validation plan (Section 12)
- 12-step build order B1-B12 (Section 13)
- Decision table I've already approved (Section 14)

**Treat the architecture doc as authoritative.** Do not invent new flow files, new gates, new sections. Do not "improve" the architecture mid-build.

## If you think the architecture needs to change

Stop. Do NOT modify it on your own. Surface the issue to me clearly:
1. What you noticed
2. Why it might be a problem
3. What change you'd suggest

Then wait. Significant deviations require running them through the **Mikoshi Protocol** for stress-testing before any change. (You'll find Mikoshi available as a skill — invoke it if needed.)

## Where the existing CoS skill lives (your reference for content)

It's installed as a plugin. Find it by searching the local filesystem for a folder named `chief-of-staff` containing `SKILL.md`. Common location pattern:

```
~/Library/Application Support/Claude/local-agent-mode-sessions/[session-id]/[id]/rpm/plugin_*/skills/chief-of-staff/
```

Use the Bash tool with `find` to locate it. Once found, that folder contains:
- `SKILL.md` (763 lines — the source content you'll be reorganizing into Zed)
- `references/` (9 files — most copy verbatim, three need light tidying per Section 7)

Treat that folder as **read-only source material**. Do NOT edit it. Zed is built fresh; CoS stays untouched until I personally retire it.

## Where to write Zed

Build the new skill inside this folder:

```
/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/zed/
```

Create that folder if it doesn't exist. The full structure (per architecture Section 4):

```
zed/
├── SKILL.md
├── flows/
│   ├── setup.md
│   ├── briefing.md
│   ├── interactive.md
│   ├── documentation-routing.md
│   ├── task-management.md
│   ├── file-cabinet.md
│   ├── watcher-setup.md
│   ├── captains-log-recall.md
│   └── alerts.md
└── references/
    ├── personality.md
    ├── signal-filters.md
    ├── platform-choice.md
    ├── captains-log.md
    ├── watcher-playbook.md
    ├── rollout-reminder.md
    ├── file-based-conventions.md
    ├── notion-conventions.md
    ├── backup-setup.md
    └── vault-safety.md
```

## The workflow — strict

Follow the architecture doc's Build Order (Section 13, steps B1-B12) in order. **Stop at every checkpoint** for my approval before moving to the next step.

Per-step protocol:

1. **Tell me what you're about to do.** One short paragraph. Reference the B-number and what it produces.
2. **Wait for my OK.** Do not proceed without it.
3. **Do the work.** Use Read, Write, Edit tools. Use Bash if needed for finding files.
4. **Summarize what you did.** Plain English. List the files created/modified.
5. **Wait for me to approve before moving to the next step.**

Do NOT batch multiple build steps without checking in. Even if a step is small.

## Pre-flight checks before B1

Before you start B1 (scaffolding), confirm out loud:

1. You've read `zed-architecture.md` end-to-end.
2. You've located the existing `chief-of-staff` plugin folder and verified you can read its SKILL.md and references.
3. The Section 14 decision table is all ✅ (it should be — I've already approved it).
4. The output folder `zed/` either exists empty or doesn't exist yet (won't accidentally overwrite anything).

Once those four are confirmed, ask: "Ready to start B1 (scaffolding)?" Wait for my OK.

## Tone and reporting style

- Plain language. No jargon without explanation.
- Short responses. No filler. No "Great question!" or "Absolutely!"
- After each build step, give me a 2-4 line summary: what was created, where, why it matches the architecture doc.
- If you hit ambiguity, ask. Don't guess.
- If something in the architecture doc seems wrong as you build, surface it before working around it.

## What "done" looks like

All 12 build steps complete. All 20 files created in the right places with the right content. The 10 tests in architecture Section 12 ready to be run (you don't run them — I do, on my live setup).

At that point you tell me: "Zed is built. Ready for testing per architecture Section 12. Recommend pausing CoS in plugin manager (Option A from Section 11) before T1."

Then we're done.

---

**Begin by reading the architecture document.**
