---
name: captains-log
description: >
  Add an entry to the Captain's Log — a personal diary for ideas, reflections, wins,
  lessons, problems, and moments worth remembering that don't belong to a specific task
  or project. ONLY trigger on EXPLICIT Captain's Log phrases: "captain's log",
  "captains log", "add to my captain's log", "captain's log entry", or when the user
  invokes /captains-log. Do NOT trigger on generic documentation phrases like "log this",
  "document this", "note this down", "record this", "journal that", "journal this",
  "remember this", or "add this to my log" — those are ambiguous and should go to the
  Chief of Staff skill so it can route the content to the right place (task, project,
  dashboard, or — as an explicit fallback — the Captain's Log). Works independently of
  the Chief of Staff skill; entries use the same schema the CoS reads.
---

# Captain's Log — Quick Entry

You are logging an entry to the user's Captain's Log. This skill works on its own — the user
does NOT need to invoke the Chief of Staff first. But entries are stored in the same format
the Chief of Staff expects, so when the CoS runs a briefing later, it picks these up automatically.

---

## Step 1 — Find the Captain's Log

You need to figure out where the user's log lives. Check in this order:

### 1a. Check for a local Chief-of-Staff folder

Look in the user's connected folders for a `Chief-of-Staff/` directory. If found:
- Check for `Chief-of-Staff/Captain's Log/` subfolder.
- If that folder exists → this is a **file-based** log (Obsidian, Logseq, or plain markdown). Go to Step 2.

### 1b. Check Notion

If no local folder, and the Notion connector is available:
- Search for a database called `Captain's Log` in the user's Notion workspace.
- If found → this is a **Notion** log. Go to Step 2.

### 1c. Neither found — minimal setup

If neither exists, the user hasn't set up their Captain's Log yet. Ask:

> "I don't see a Captain's Log set up yet. Where would you like it?"
>
> 1. **Notion** — I'll create a Captain's Log database in your Notion workspace
> 2. **Local folder** — I'll create it in a folder on your computer (works with Obsidian, Logseq, or any markdown viewer)

Then create the log:

**Notion:** Create a database called `Captain's Log` at the workspace top level with this schema:
- `Title` — title property
- `Date` — date property
- `Type` — select: Milestone (green), Decision (blue), Win (yellow), Learning (purple), Problem (red), Idea (orange)
- `Project` — multi_select (start empty — projects get added as the user logs entries)
- `Details` — rich text

Add three views: **Recent** (Date within last 30 days, sort descending), **By Type** (group by Type), **By Project** (group by Project).

**Local folder:** Use `request_cowork_directory` to get a folder, then create `Captain's Log/` inside it. Entries will be markdown files.

After creating, proceed to Step 2 with the user's original message.

---

## Step 2 — Parse the entry

Read the user's message (and recent conversation context if needed) to extract:

- **What happened** — the core event, accomplishment, or thought.
- **Project** — which project this relates to. If the user has a Chief of Staff State Dashboard, use the Active Projects list from there. Otherwise, infer from context or leave blank. Common projects might include things like the user's company name, product names, or "Personal."
- **Type** — categorize the entry:
  - `Milestone` — a significant checkpoint, release, launch, or completed major work
  - `Decision` — a choice was made about direction or approach
  - `Win` — something worked, a success, positive feedback
  - `Learning` — something discovered, a lesson learned, a gotcha
  - `Problem` — a bug, issue, or blocker encountered
  - `Idea` — a future possibility, brainstorm, or "what if"

If the conversation context makes the project or type obvious, use it. Don't over-ask — just pick the best fit and tell the user what you picked so they can correct it.

---

## Step 2.5 — Cross-check before logging

After parsing, **actively search** the user's operational databases before writing anything.
Do not rely on whether the content "sounds" operational — check the data.

### Required searches

Run these searches using keywords extracted from the parsed entry (e.g., for a car mileage
entry, search "car", "mileage", "odometer", "vehicle"):

1. **Tasks database** — search for any open task related to the entry's subject.
2. **State Dashboard** — search for any Watch List item, calendar anchor, or active
   project note that references the same subject.

If either search returns a match, **stop and tell the user** before logging:

> "I found a related [task / watch list item / etc.]: **[title of the match]**. This
> might be better handled as a [task update / status change / etc.] through your Chief
> of Staff so it stays connected. Want me to route it there, or log it here anyway?"

If the user says "log it anyway" → proceed to Step 3.
If the user wants the Chief to handle it → tell them to invoke the Chief of Staff
(by its name or by saying "briefing") and reference what they wanted documented.

### Skip conditions

Skip the search only if:
- The user has no Chief of Staff set up (no Tasks database or State Dashboard exists).
- The entry is explicitly abstract — a pure idea, philosophical reflection, or personal
  feeling with no possible operational connection.

### If no matches found

Proceed to Step 3 normally. The entry belongs in the Captain's Log.

---

## Step 3 — Write the entry

### Notion

Use the Notion `create-pages` tool. The parent should reference the Captain's Log database (find it by searching or use a stored database/data source ID if available).

Properties to set:
- **Title:** A clear, short summary (under 10 words)
- **Date:** Today's date (or the date the user specifies — "yesterday", "last Tuesday", etc.)
- **Type:** The detected type (as a select value)
- **Project:** The detected project(s) (as a multi_select array)

If the entry warrants more detail (bug fix with root cause, decision with reasoning, etc.), add body content using the `content` field. Keep it brief — a few sentences or short bullet points.

### File-based (Obsidian / Logseq / plain markdown)

Create a markdown file in the `Captain's Log/` folder.

**Filename:** `YYYY-MM-DD-short-slug.md` (e.g. `2026-04-20-workshop-bug-fixed.md`)

**Contents:**

```markdown
---
type: captains-log
date: YYYY-MM-DD
log_type: Win
project: [Project Name]
---

# Short title here

Details — what happened, why it matters, any links or context.
```

### Logseq (if detected)

If the user's platform is Logseq, append a block to the `Captain's Log` page instead of creating a file:

```
- Short title here
  type:: captains-log
  date:: YYYY-MM-DD
  log-type:: Win
  project:: [[Project Name]]
  - Details go in a child block.
```

---

## Step 4 — Confirm

Tell the user what you logged in one short line, like:

> Logged: **Workshop button bug fixed** (Win · Sage & Savvy)

If you had to guess the Type or Project, say what you picked:

> Logged: **Switched to reusable payment links** (Decision · Crunchy Buzz) — change the type or project if that's not right.

Include a link to the Notion page if applicable. Don't over-explain.

---

## Notes

- **No setup required to use this skill.** If the Captain's Log doesn't exist yet, the skill creates it on the fly (Step 1c). The full Chief of Staff setup is NOT a prerequisite.
- **Compatible with Chief of Staff.** Entries written by this skill use the exact same schema the CoS reads during briefings. If the user later sets up or invokes the Chief of Staff, all their log entries show up in the "Recent Log" section automatically.
- **One entry per invocation.** If the user rattles off multiple things, ask if they want separate entries or one combined entry.
- **Date flexibility.** Default to today, but respect "yesterday", "last week", "on March 12", etc.
- **Don't editorialize.** Write what the user said, not your interpretation. Keep Details factual and concise.

---

## Stable Names — Do Not Change Without Migration

This skill finds the user's log by searching for specific names. These must stay consistent across versions — see the matching section in the Chief of Staff SKILL.md for the full list and migration rules.

### Names this skill depends on:
- **Notion:** Database titled `Captain's Log` with properties: `Title` (title), `Date` (date), `Type` (select), `Project` (multi_select), `Details` (rich text)
- **File-based:** Folder named `Captain's Log/` inside `Chief-of-Staff/`. Files use frontmatter keys: `type: captains-log`, `date`, `log_type`, `project`
- **Logseq:** Page named `Captain's Log`. Block properties: `type::`, `date::`, `log-type::`, `project::`

If a future version changes any of these names, Step 1 must check for both old and new names to avoid breaking existing users.
