---
name: log
description: >
  Quick documentation router — figures out where something you want to document
  should go (task update, project note, State Dashboard, or Captain's Log) and
  writes it there. Triggers on ALL quick-documentation phrases: "log this",
  "log: …", "document this", "note this down", "record this", "journal that",
  "journal this", "remember this", "capture this", "add this to my log",
  "captain's log", "captains log", "captain's log: …". Fast to start — no
  briefing, no full State Dashboard load. Just
  a targeted keyword search of your Tasks and State Dashboard, then a routed
  write to the right place. The Captain's Log is still a first-class destination
  for reflective or personal content — but it's no longer the default dump. If
  the Chief of Staff is already running (briefing or interactive session), it
  routes documentation with its own internal logic; this skill is the lightweight
  alternative for cold starts.
---

# Log — Quick Documentation Router

You are routing a piece of documentation to the right place. This skill works on its own —
the user does NOT need to invoke the Chief of Staff. It's fast: a targeted keyword search,
then a write to whichever destination makes sense.

Your job: parse → search → route → write → confirm. Keep it quick.

---

## Step 1 — Find the user's storage locations

You need to know where four things live: the Captain's Log, the Tasks database,
the State Dashboard, and (if relevant) the Projects database. Check platform:

### 1a. File-based (Obsidian / Logseq / markdown folder)

Look for a `Chief-of-Staff/` folder in the user's connected folders.
- Captain's Log: `Chief-of-Staff/Captain's Log/`
- Tasks: `Chief-of-Staff/Tasks/`
- State Dashboard: `Chief-of-Staff/State Dashboard.md`
- Projects: `Chief-of-Staff/Projects/`

### 1b. Notion

If the Notion connector is available:
- Captain's Log: top-level `Captain's Log` database
- Tasks: `Tasks` database under the `Chief of Staff` parent page
- State Dashboard: `State Dashboard` page under the `Chief of Staff` parent page
- Projects: `Projects` database under the `Chief of Staff` parent page

### 1c. Nothing found — minimal setup

If neither a local folder nor a Notion parent page exists, the user hasn't set up
their Chief of Staff yet. Ask:

> "I don't see a Captain's Log or Chief of Staff area set up yet. Where would you
> like your log to live?"
>
> 1. **Notion** — I'll create a Captain's Log database in your Notion workspace
> 2. **Local folder** — I'll create it in a folder on your computer

Then create ONLY the Captain's Log (same as the old skill did — see
`../chief-of-staff/references/captains-log.md` for schema and layout).
Since there's no Tasks or State Dashboard in this scenario, skip Step 3's
search and proceed directly to writing the entry in the Captain's Log.

---

## Step 2 — Parse what the user wants to document

Extract from the user's message (and recent conversation if needed):

- **What happened** — the core event, fact, update, or thought.
- **Keywords** — 3–5 distinctive words that could match a task or dashboard item
  (e.g., for "my car mileage is 112560": `car`, `mileage`, `odometer`, `vehicle`).
- **Likely type** — is this operational (a status update, a fact about a thing the
  user is tracking) or reflective (a win, lesson, idea, decision)? This is a first
  guess — the search in Step 3 confirms it.
- **Project** — which project this relates to, if obvious from context. Leave blank
  if unclear.

Don't over-ask. First guess, then confirm after routing.

---

## Step 3 — Search for related operational records

This is the core routing step. Before writing anything, search the user's
operational databases using the keywords from Step 2.

### Required searches

1. **Tasks database** — search for open tasks (Status = to-do / in-progress /
   blocked) whose Name, Goal, or Notes match any of the keywords.
2. **State Dashboard** — search Watch List, Active Projects, and Open Decisions
   for entries matching the keywords.

Keep the searches targeted. Do NOT load the whole State Dashboard or dump all
tasks — just match on keywords.

### Skip conditions

Skip the search only if:
- The user has no Chief of Staff set up (Step 1c path — only a Captain's Log
  exists).
- The content is explicitly abstract or reflective (a pure idea, philosophical
  note, personal feeling) with no possible operational hook. Use judgment — when
  in doubt, search.

---

## Step 4 — Route to the right destination

Based on what the search found, pick one of four destinations:

### Route A — Matching open task found

The content updates or completes an existing task. Offer to update the task
instead of logging:

> "Looks like this relates to the task **[Task Name]**. Want me to add this as a
> note on the task (or mark it done, if that's what this is) instead of logging
> it? Or log it anyway?"

If the user says yes → update the task (add to Notes, or change Status). If the
user says log it anyway → go to Route D.

### Route B — Matching watch list / project / dashboard item found

The content is an update to something the user is tracking. Offer to update that
record:

> "This matches your watch list item **[Item]** — want me to update that
> record, or log it in the Captain's Log?"

If the user picks update → write to the matching record (append to Notes,
update a field, whatever fits). If the user picks log → go to Route D.

### Route C — No match, but unclear destination

The search returned nothing, but the content doesn't feel obviously reflective.
Present 2–3 options:

> "Not sure where this fits. Options:
> 1. New task under [likely project]
> 2. Note on [Project X]
> 3. Captain's Log entry ([Type] · [Project])
> Which one?"

Always include Captain's Log as one option. Write to whichever the user picks.

### Route D — Captain's Log (default for reflective content)

No operational match, and the content is reflective or personal (an idea, win,
lesson, decision, problem, milestone). Write to the Captain's Log. Classify by
Type per the schema in `../chief-of-staff/references/captains-log.md`:

- `Milestone` — releases, launches, completed major work
- `Decision` — choices made and why
- `Win` — something good worth remembering
- `Learning` — insight, lesson learned
- `Problem` — issue encountered, noted for later
- `Idea` — something to maybe do later

---

## Step 5 — Write

Write to whichever destination Step 4 picked. Use the platform rules that apply.

### Captain's Log writes

Use the format in `../chief-of-staff/references/captains-log.md` (entry schema,
Notion / Obsidian / Logseq / markdown-folder layouts, filename rules).

### Task writes

- **Notion:** update the Task record's Notes property (append, don't overwrite)
  or its Status field if the user asked to complete it.
- **File-based:** edit the task file in `Chief-of-Staff/Tasks/` — append to the
  notes section below the frontmatter, or update `status:` in frontmatter.

### Project / Dashboard writes

- **Notion:** update the relevant Project record's Notes, or the State
  Dashboard page's Watch List section.
- **File-based:** edit the project file in `Chief-of-Staff/Projects/` or the
  `State Dashboard.md` — append to the appropriate section.

---

## Step 6 — Confirm in one line

Short confirmation, adapted to destination:

- **Task update:** "Updated task: **[Task Name]** — added note about [what]."
- **Status change:** "Marked **[Task Name]** as done."
- **Project / dashboard update:** "Updated **[Project X]** — noted [what]."
- **Captain's Log:** "Logged: **[Title]** ([Type] · [Project])."

If you had to guess Type or Project for a Captain's Log entry, say what you
picked so the user can correct it.

Include a link to the record if the platform supports it.

---

## Notes

- **Fast by design.** Don't load the full State Dashboard. Don't pull all tasks.
  Just do targeted keyword matches, then write.
- **One entry per invocation.** If the user rattles off multiple things, ask
  whether they want separate entries or one combined.
- **Captain's Log is a first-class destination — not deprecated.** When content
  is reflective or personal, the Log is the right place. This skill just no
  longer sends everything there by default.
- **Chief of Staff compatibility.** If the Chief of Staff is already running,
  it uses its own internal routing logic (unchanged). This skill is for cold
  starts — when the user wants to document something quickly without a full
  briefing session.
- **Don't editorialize.** Write what the user said, not your interpretation.
- **Date flexibility.** Default to today, but respect "yesterday", "last week",
  "on March 12", etc.

---

## Stable Names — Do Not Change Without Migration

This skill finds data by looking for specific names that must stay consistent
with what the Chief of Staff skill uses. See the Stable Names section in the
Chief of Staff `SKILL.md` for the full list and migration rules.

### Names this skill depends on:

- **All platforms:** `Chief of Staff` (Notion parent page) or `Chief-of-Staff/`
  (local folder)
- **Captain's Log:**
  - Notion: database titled `Captain's Log` at workspace top level
  - File-based: folder `Captain's Log/` inside `Chief-of-Staff/`
  - Logseq: page `Captain's Log`
  - Captain's Log entry properties: `Title`, `Date`, `Type`, `Project`,
    `Details` (Notion); frontmatter keys `type: captains-log`, `date`,
    `log_type`, `project` (file-based); block properties `type::`, `date::`,
    `log-type::`, `project::` (Logseq)
- **Tasks:**
  - Notion: `Tasks` database under `Chief of Staff` parent, properties `Name`,
    `Status`, `Priority`, `Due Date`, `Project`, `Goal`, `Notes`, `Waiting On`
  - File-based: folder `Tasks/` inside `Chief-of-Staff/`, frontmatter `type:
    task`, `status`, `priority`, `due`, `project`, `goal`, `waiting-on`
- **Projects:**
  - Notion: `Projects` database under `Chief of Staff` parent, properties
    `Name`, `Department`, `Status`, `Started`
  - File-based: folder `Projects/` inside `Chief-of-Staff/`
- **State Dashboard:**
  - Notion: `State Dashboard` page under `Chief of Staff` parent
  - File-based: `Chief-of-Staff/State Dashboard.md`
