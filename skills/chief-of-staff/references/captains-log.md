# Captain's Log

A structured personal log for the user — milestones, decisions, wins, learnings, problems, and ideas. Whatever they want to be able to recall later.

**Captain's Log is mandatory.** Every Chief of Staff setup creates it during S4, regardless of platform.

---

## When to log

The user creates entries by saying things like:

- "Log this: ..."
- "Captain's Log: ..."
- "Journal that ..."
- "Remember this ..."

Treat any of these as a trigger to create one new Captain's Log entry. Confirm the entry briefly after writing.

**Do not proactively log on the user's behalf** (with one exception — see "Setup behavior" below). Captain's Log is the user's voice. Don't dump briefings, State Dashboard updates, or alert noise in here — those have their own homes.

---

## Entry schema (same on every platform)

Every entry has 5 fields:

1. **Title** — short one-line summary.
2. **Date** — date the event happened. Default to today; let the user override if they say "yesterday", "last week", "March 12", etc.
3. **Type** — exactly one of:
   - `Milestone` — releases, launches, completed major work
   - `Decision` — choices made and why
   - `Win` — something good worth remembering
   - `Learning` — insight, lesson learned
   - `Problem` — issue encountered, noted for later
   - `Idea` — something to maybe do later
4. **Project** — zero or more, drawn from the user's Active Projects in the State Dashboard, plus `Personal` and `Other`.
5. **Details** — full context, story, links, commands. Free text.

If the user doesn't specify Type or Project, infer from context — but tell them what you picked so they can correct it: "Logged as a Milestone tagged Chief of Staff. Sound right?"

---

## Where Captain's Log lives — by platform

### Notion

A single Notion database called `Captain's Log` at the **workspace top level** (NOT under the Chief of Staff parent page — kept top-level so the user can find it independently).

Schema:

- `Title` — title
- `Date` — date
- `Type` — select: `Milestone`:green, `Decision`:blue, `Win`:yellow, `Learning`:purple, `Problem`:red, `Idea`:orange
- `Project` — multi_select, built from the user's Active Projects in the State Dashboard, plus `Personal`:gray and `Other`:default
- `Details` — rich text

Three default views:

- **Recent** — filter Date within last 30 days, sort Date descending. This is the default view.
- **By Type** — group by Type.
- **By Project** — group by Project.

### Obsidian

A folder `Chief-of-Staff/Captain's Log/` with one markdown file per entry.

Filename: `YYYY-MM-DD-short-slug.md` (e.g. `2026-04-20-published-cos-skill.md`).

Each file uses YAML frontmatter:

```yaml
---
type: captains-log
date: 2026-04-20
log_type: Milestone        # one of Milestone / Decision / Win / Learning / Problem / Idea
project: [Chief of Staff]  # array — zero or more
cos: true
---

# Title goes here

Free-text details, links, story.
```

If the user has the Dataview plugin installed, an optional `_index.md` inside the folder can render "Recent" / "By Type" / "By Project" views using Dataview inline queries. Without Dataview, plain folder browsing still works.

### Logseq

A dedicated page called `Captain's Log` in the user's graph. (If the user picked `namespaces` placement during Logseq setup, the page is `Chief-of-Staff___Captain's Log` per Logseq's namespace convention.)

Each entry is a top-level block with properties:

```
- Title goes here
  type:: captains-log
  date:: 2026-04-20
  log-type:: Milestone
  project:: [[Chief of Staff]]
  cos:: true
  - Free-text details / story / links go in a child block.
```

Logseq's built-in queries filter by `log-type` or `project`. Add a few starter queries to the page so the user gets "Recent", "By Type", and "By Project" views out of the box.

### Plain markdown folder

Same as Obsidian — a `Chief-of-Staff/Captain's Log/` folder with dated files and YAML frontmatter. No query views — filtering is by file browsing or full-text search.

---

## Setup behavior (during S4)

After the main Chief of Staff structure is created, also create the Captain's Log:

- **Notion** → create the `Captain's Log` database at the workspace top level. Apply the schema above. Create the three default views (Recent, By Type, By Project).
- **Obsidian / markdown-folder** → create the `Chief-of-Staff/Captain's Log/` folder (empty, ready for entries).
- **Logseq** → don't create the page proactively — just record in the State Dashboard that Captain's Log lives at the page `Captain's Log` (or the namespace path if `namespaces` placement). The page is created on first entry.

**Then write the first entry yourself.** This is the ONE auto-generated entry the system creates:

- Title: "Chief of Staff set up"
- Date: today
- Type: `Milestone`
- Project: `Chief of Staff`
- Details: short summary — platform chosen, date, anything notable from setup

This proves the system works AND gives the user one real example to look at.

After writing it, tell the user where Captain's Log lives and how to add entries:

> "Captain's Log is set up. To add an entry, just say 'log this: …' or 'captain's log: …' and I'll write it down. I already added one entry to mark today's setup."

---

## Reading from Captain's Log (during briefings)

When generating a daily briefing, query Captain's Log for entries from the last 7 days. If any exist, add this section near the bottom of the briefing (after `RECOMMENDED ACTIONS`, before `ROLLOUT NUDGE`):

```
RECENT LOG (last 7 days):
- [Date] [Type] — Title
- [Date] [Type] — Title
```

Titles only — no details. The user can open the entry if they want the full story.

If no entries in the last 7 days, omit the section entirely (no empty header).

---

## Confirmation pattern (after logging)

Confirm in one short line:

- **Notion:** "Logged: 'Published Chief of Staff skill to GitHub' (Milestone, Chief of Staff). [Open entry](url)"
- **File-based:** "Logged: 'Published CoS skill to GitHub' → `Chief-of-Staff/Captain's Log/2026-04-20-published-cos-skill.md`"
- **Logseq:** "Logged: 'Published CoS skill to GitHub' → `Captain's Log` page."

If you had to infer Type or Project, name what you picked so the user can correct it.

---

## Editing past entries

If the user says "update the [topic] entry to add..." or "fix yesterday's log — it should say...":

1. Find the entry by Title or Date.
2. Make the edit (do not snapshot — Captain's Log entries are not part of State Dashboard).
3. Confirm: "Updated: '[Title]' — added [what you added]."

If multiple entries match, list them and ask which one.

---

## Recall

When the user asks recall questions like:

- "What did I log about GitHub?"
- "Show me all my wins from this month."
- "Pull up anything tagged Sage & Savvy."
- "What did I decide about pricing?"

Query Captain's Log with the appropriate filter (full-text on Title/Details, or Type/Project/Date filters), return matching entries' Title + Date + Type + a one-line snippet of Details. Link to each.
