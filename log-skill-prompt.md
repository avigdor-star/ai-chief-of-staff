# Prompt: Replace the Captain's Log Skill with a New "Log" Documentation Router

## Context — What You're Working On

You are editing the **AI Chief of Staff** plugin — a Claude Code / Cowork plugin that gives users a personal chief of staff for daily operations, prioritization, and situational awareness. The plugin lives in a local Git repo. You must use the **cos-dev** skill to make all changes (it enforces a safe, step-by-step editing process — read before edit, propose before apply, one change at a time, log everything).

The plugin repo is at:
```
/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/
```

The plugin currently has two skills:
1. **chief-of-staff** — the full operational system (briefings, task management, source scanning, documentation routing). Heavy, takes time to start up.
2. **captains-log** — a lightweight standalone skill for quick personal logging (milestones, decisions, wins, learnings, problems, ideas). Fast to start up but has no routing intelligence.

You are **replacing the captains-log skill** with a new skill called **log**. The Captain's Log *database* stays — it's a valid destination. What changes is the *skill* that decides where documentation goes.

---

## The Problem — Why This Change Is Needed

We discovered a fundamental architecture flaw through a real incident:

**What happened:** The user told their Chief of Staff (named "Zed"): "zed, my car mileage is 112560." Claude triggered the captains-log skill instead of the chief-of-staff skill. The captains-log skill logged it as a personal milestone in the Captain's Log database. But there was already an open task in the Tasks database ("Add odometer reading to car maintenance log") and a watch list item on the State Dashboard ("Subaru mileage for radiator replacement — update the Airtable maintenance log once confirmed"). The mileage entry should have been routed to the task, not silently dumped into the Captain's Log.

**Root cause analysis (Mikoshi Protocol stress test):**

1. **Skill routing is a judgment call with no enforcement.** Claude decides which skill to trigger based on keywords and vibes. "Zed" is the Chief of Staff's name — it should have triggered the chief-of-staff skill. The captains-log skill description explicitly says it should ONLY trigger on explicit "captain's log" phrases. But Claude picked the wrong skill, and nothing stopped it.

2. **The Captain's Log skill bypasses the Chief of Staff's routing intelligence.** The Chief of Staff has a sophisticated documentation routing system (operational first → ask if unsure → Captain's Log as last resort). The captains-log skill skips all of that. Any time Claude mistakenly triggers it, the routing brain gets bypassed entirely.

3. **Skills can't invoke each other.** Even after we added an active cross-reference search (Step 2.5) to the captains-log skill, it can only tell the user "go invoke the Chief of Staff yourself." It can't hand off automatically. The user has to manually trigger the Chief in a separate message.

4. **The Captain's Log skill is never standalone.** It ships inside the Chief of Staff plugin — the Chief of Staff always exists alongside it. So the "works independently" design premise is false. There's no use case where the Captain's Log skill runs without the Chief of Staff being available.

5. **The cross-reference patch is duct tape.** We added a Step 2.5 that searches Tasks and State Dashboard before logging. But this means the Captain's Log skill is now rebuilding a piece of the Chief's routing logic inside a separate skill — doing the Chief's job with less capability.

**CIQ Score of current architecture: 66/100 (failing).** Weak on depth (no fallback when wrong skill is picked), practicality (fails silently on misfires), and logical soundness (two skills with overlapping domains and no enforcement).

---

## The Solution — What the New "Log" Skill Does

The new **log** skill is a **lightweight documentation router**. Same speed as the old Captain's Log (fast startup, no briefing overhead), but with routing intelligence baked in. It replaces the Captain's Log skill entirely.

### Core behavior:

1. **Parse** what the user wants to document (same as old Step 2 — extract the what, the project, the type).

2. **Search** the user's operational databases for related items (the active cross-reference from Step 2.5 — search Tasks database and State Dashboard using extracted keywords).

3. **Route** to the right destination:
   - If a related task is found → offer to update that task instead of logging
   - If a related project/department/watch list item is found → offer to update that record
   - If no operational match and the content is reflective/personal → write to the Captain's Log
   - If unclear → present 2–3 options and let the user pick (always include Captain's Log as one option)

4. **Write** to whichever destination the routing selects — the skill can write to Tasks, Projects, State Dashboard, AND the Captain's Log. It doesn't need to hand off to the Chief of Staff. It has the Notion tools (or file tools) to make the update directly.

5. **Confirm** briefly — same pattern as before ("Logged: ... " or "Updated task: ...").

### What this solves:

- **No more bypass.** The routing intelligence is built into the lightweight skill, so even if Claude picks the log skill instead of the Chief, the user's content still gets routed correctly.
- **No manual handoff.** The skill can write to operational databases itself — no need to tell the user "go invoke the Chief."
- **Still fast.** No briefing, no source scanning, no State Dashboard loading (beyond a targeted keyword search). Quick in, quick out.
- **Chief of Staff compatibility.** When the Chief of Staff is already running and encounters something to document, it uses its own internal routing logic (unchanged). The log skill is for when the Chief isn't running.

### Trigger phrases:

The log skill should trigger on:
- "log this", "log: ...", "document this", "note this down", "record this", "journal that", "journal this", "remember this", "capture this", "add this to my log"
- "captain's log", "captains log", "captain's log: ..."
- Any phrase that implies "I want to document/record something quickly"

This is a CHANGE from the current architecture where generic phrases went to the Chief of Staff and only explicit "captain's log" phrases went to the captains-log skill. Now the log skill handles ALL quick documentation — it has the routing brains to do it right.

**Important:** The Chief of Staff skill description must be updated to REMOVE generic documentation phrases from its trigger list, since the log skill now handles those. The Chief of Staff should still handle documentation routing when it's already running (during a briefing, interactive session, etc.) — but it should NOT be the skill that triggers on "log this" or "document this" from a cold start.

---

## Execution Plan — Step by Step

Follow the **cos-dev** skill process for every file change (read → check fit → propose → get approval → apply → log). Here's the order:

### Step 1: Create the new skill

Create `skills/log/SKILL.md` with the behavior described above. Pull in what's useful from the current captains-log skill:
- Step 1 (finding the Captain's Log location) — keep but expand to also locate the Tasks database and State Dashboard
- Step 2 (parsing) — keep as-is
- Step 2.5 (cross-reference search) — keep but expand: instead of just flagging matches and telling the user to invoke the Chief, the skill now routes and writes directly
- Step 3 (writing) — keep the Captain's Log writing logic, but ADD the ability to write task updates, project notes, and State Dashboard updates
- Step 4 (confirmation) — keep, adapt for different destination types
- Notes section — update to reflect new routing behavior
- Stable Names — keep all existing stable names (Captain's Log database names, property names, frontmatter keys are UNCHANGED)

The skill description (frontmatter) should clearly define trigger phrases and explicitly state that this skill handles ALL quick documentation routing.

Key design principles for the new skill:
- **Fast.** Don't load the full State Dashboard. Do targeted keyword searches only.
- **Smart.** Search before writing. Route to the right place.
- **Self-sufficient.** Can write to Tasks, Projects, Captain's Log — doesn't need to hand off to the Chief.
- **User-agnostic.** No personal details in the skill text. Generic examples only.
- **Captain's Log is still a first-class destination.** It's not deprecated — it's just no longer the only destination this skill knows about.

### Step 2: Update the Chief of Staff skill

Edit `skills/chief-of-staff/SKILL.md`:

**a) Update the description frontmatter (lines 1–19):**
- Remove generic documentation phrases ("log this", "document this", "record this", etc.) from the Chief of Staff's trigger list
- Remove the sentence about the captains-log skill handling explicit phrases
- Add a note that the **log** skill handles quick documentation routing
- The Chief of Staff should still trigger on operational/briefing phrases — just not documentation phrases

**b) Update the documentation routing section (around lines 528–554):**
- This section currently says "when the user says 'log this'..." — update it to clarify that this routing logic applies WHEN THE CHIEF IS ALREADY RUNNING (during a briefing or interactive session). For cold-start documentation, the log skill handles it.
- Keep the routing logic itself (operational first → ask if unsure → Captain's Log as fallback) — it's still correct for when the Chief is active
- Update the reference from "captains-log skill" to "log skill"

**c) Update proactive documentation sections (around lines 476–493 and 556+):**
- These describe how the Chief suggests documentation during briefings — keep them, they're about the Chief's own behavior when it's already running
- Update any references to the "captains-log skill" to say "log skill"

**d) Update the Reference Files section (around line 710):**
- The `references/captains-log.md` entry should note that this reference is used by both the Chief of Staff AND the log skill

**e) Update S4.5 (around line 227–247):**
- This is the setup step that creates the Captain's Log. Keep it — the Captain's Log database still gets created during setup. Just update any references to the "captains-log skill" to say "log skill"
- Update the user-facing message about how to add entries (currently says "say 'log this: ...' or 'captain's log: ...'") — this should now reference the log skill's behavior

### Step 3: Delete the old skill

Delete the `skills/captains-log/` folder entirely. The new `skills/log/` replaces it.

### Step 4: Update the captains-log.md reference file

Edit `skills/chief-of-staff/references/captains-log.md`:
- This file defines the Captain's Log database schema, entry format, and platform-specific storage. It stays.
- Update any text that references the "captains-log skill" to say "log skill"
- The trigger phrases section (lines 15–21) should be updated to reflect that these phrases now trigger the log skill, which routes content to the Captain's Log when appropriate
- The "Do not proactively log" rule still applies — but now it applies to the log skill, not the captains-log skill

### Step 5: Update plugin metadata

**a) `.claude-plugin/plugin.json`** — no skill listings here (it's just name/version/description), but check if anything references captains-log

**b) `README.md`** — update:
- The feature list (Captain's Log description → log skill description)
- The skill table (captains-log row → log row with updated description)
- The directory tree (captains-log/ → log/)
- Version notes

### Step 6: Log to CHANGELOG.md

Add a comprehensive changelog entry documenting:
- What: Replaced captains-log skill with log skill (documentation router)
- Why: Architecture flaw — separate logging skill bypassed Chief of Staff routing intelligence
- What changed: new skill with routing, Chief of Staff trigger list updated, old skill deleted

### Step 7: Verify

After all changes are applied:
- Search the entire repo for any remaining references to "captains-log" (the old skill name) — there should be none except in CHANGELOG.md (historical entries)
- Verify that `references/captains-log.md` still exists and is referenced correctly
- Verify that the Chief of Staff's Stable Names section still lists the Captain's Log database names
- Verify that no personal/session-specific details leaked into the skill text

---

## Important Constraints

1. **The Captain's Log DATABASE is unchanged.** Same name, same schema, same properties, same location (Notion top-level / file-based subfolder / Logseq page). The log skill writes to it the same way the old skill did. Users' existing Captain's Log entries are unaffected.

2. **The `references/captains-log.md` file stays.** It defines the database format. Both the log skill and the Chief of Staff reference it.

3. **Stable Names are sacred.** Do not rename any database, property, folder, or frontmatter key listed in the Stable Names section. The log skill must find things using the same names the old skill used.

4. **User-agnostic text only.** No real project names, no personal details, no session-specific data in any skill file. Use generic examples (e.g., "Project X", "your 2 PM meeting", "the deploy").

5. **The Chief of Staff's internal documentation routing logic is unchanged.** When the Chief is already running, it routes documentation using its own logic. The log skill is the lightweight alternative for cold starts.

6. **Follow the cos-dev process.** Read before editing. Propose before applying. One change at a time. Log to CHANGELOG.md.

---

## Files to Read Before Starting

Read these files in full before making any changes — they contain the current state of everything you'll be editing:

1. `/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/skills/captains-log/SKILL.md` (the skill you're replacing)
2. `/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/skills/chief-of-staff/SKILL.md` (the skill you're updating)
3. `/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/skills/chief-of-staff/references/captains-log.md` (the reference file that stays)
4. `/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/README.md`
5. `/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/CHANGELOG.md`
6. `/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/.claude-plugin/plugin.json`
7. `/Users/avigdorlevi/Avigdor's Vault/Projects/Chief of Staff/skill/.claude-plugin/marketplace.json`

Also invoke the **cos-dev** skill at the start of your session — it contains the editing process you must follow for every change.
