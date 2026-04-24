# Chief of Staff — Changelog

Changes to the skill during beta development. Newest first.

---

## 2026-04-23

### "File cabinet" introduced as user-facing term for the four-level hierarchy
- **Files:** skills/chief-of-staff/SKILL.md, skills/chief-of-staff/references/notion-conventions.md, skills/chief-of-staff/references/file-based-conventions.md
- **What:** Updated the glossary, the smart nesting helper intro, and the linking-chain descriptions in both convention files to lead with "file cabinet" instead of "four-level hierarchy." Parentheticals like "(four levels)" or "(four-level hierarchy)" kept inline so readers still see the structural definition. Underlying hierarchy (Domain > Department > Project > Task) and the technical vocabulary are unchanged.
- **Why:** "File cabinet" is a plainer mental model than "four-level hierarchy." Easier to talk about, easier to remember, friendlier for non-technical users. The technical phrasing stays available as a definition, but "file cabinet" becomes the primary concept name.

### Smart nesting (file-cabinet helper) added
- **File:** skills/chief-of-staff/SKILL.md
- **What:** New Mode B sub-section teaches the Chief to infer record type (task / project / department / domain) from user language, then offer to nest the record in the four-level hierarchy — auto-creating missing parents if the user wants, skipping the nesting entirely if they don't. A dangling record is allowed; it still works and surfaces in briefings. New Mode A section ("File Cabinet Check," section 11, before Rollout Nudge) runs a soft nudge when unnested records exist — framed as a helpful offer, never a violation.
- **Why:** Tasks arrive in all shapes. "I need diapers" is a task; "I want to build a car maintenance system" is a project. The Chief should read intent and propose the right home — without forcing structure. Dangling records get noticed later via a gentle nudge, not flagged at session start.

### Tag vocabulary removed — skill goes silent on tags
- **Files:** skills/chief-of-staff/SKILL.md, skills/chief-of-staff/references/file-based-conventions.md, skills/chief-of-staff/references/notion-conventions.md
- **What:** Removed all prescribed tag vocabulary. Deleted the required `tags: [cos, ...]` lines from every frontmatter template, dropped the Tags row from every Notion database schema, removed the stand-alone Tags sections in both reference files, and pulled the `cos` tag checks out of the Axiom 2 structure drift check. The Tags property on Notion databases and the `tags:` field in YAML frontmatter still exist as user-owned space — the skill just stops dictating vocabulary.
- **Why:** Tagging is user-specific. Different users have different mental models and needs. The skill should be agnostic about tags so users can build whatever vocabulary fits their work.

### Documentation routing fix — Captain's Log is fallback, not default
- **Files:** skills/captains-log/SKILL.md, skills/chief-of-staff/SKILL.md
- **What:** Tightened the captains-log skill's triggers so it ONLY fires on explicit Captain's Log phrases ("captain's log", "add to my captain's log", /captains-log). Generic documentation phrases ("log this", "document this", "record this", "note this down", "remember this", "journal that", "journal this", "capture this") no longer trigger the captains-log skill — they now go to the Chief of Staff, which applies a strict routing rule: try operational first (Task, Project, Department, Watch List, State Dashboard) → if unsure, ALWAYS ask (offer 2–3 specific options) → Captain's Log only as an explicit fallback or user pick. Explicit "captain's log" phrases still bypass routing and write directly.
- **Why:** Captain's Log was becoming the default dump for anything the user asked to "document" or "log," even when the content belonged in a task, project, or dashboard record. The two trigger surfaces (captains-log skill description + Chief of Staff skill description) both grabbed generic phrases, and the Mode B instruction explicitly mapped "log this" → Captain's Log entry. Users lost the distinction between "update my work records" and "write in my personal diary." New behavior: operational records are the primary destination; Captain's Log is the explicit, intentional diary.

---

## 2026-04-22

### Section-by-section briefing flow (replaces the full-dump format)
- **File:** skills/chief-of-staff/SKILL.md
- **What:** Mode A (Briefing) no longer dumps the entire briefing at once. Sections are now delivered one at a time, strictly linear, with a tailored question after each section and a "Can we move on to the next section?" gate before advancing. Mid-section actions get queued ("Queued for after the brief") and run as a batch after the final section. Empty sections are announced ("Messages: nothing") except for Rollout Nudge, which stays silent when not eligible. Alerts section is now system-level only (backup, structure drift, connector failures) — personal watch-list items stay in the Watch List section. Backup alert now proposes a specific free calendar slot and offers to book it. "Recent Log" section removed. New section order: Alerts → Carryover → Schedule → Top 3 → Tasks → Messages → Missed Message Check → Watch List → Risks/Blockers → Recommended Actions → Rollout Nudge.
- **Why:** A single-dump briefing gave the user no room to think through items or act on them before the next section loaded — it was a brain-dump, not a briefing. Drip delivery with per-section questions makes the briefing a working session instead of a wall of text. Queueing mid-section actions keeps momentum while still letting the user act on things in context.

### Separated documentation routing: operational databases vs. Captain's Log
- **Files:** skills/chief-of-staff/SKILL.md, skills/chief-of-staff/references/captains-log.md, skills/captains-log/SKILL.md
- **What:** Proactive documentation now routes items to the right place — task/project/dashboard updates happen automatically, but Captain's Log entries are never auto-written. The Chief explains why something seems like a log entry and lets the user decide. Captain's Log redefined as a "personal memory bank" for ideas, reflections, wins, and lessons — not operational status updates. Standalone Captain's Log skill now acts as a gatekeeper: if content looks operational, it suggests routing through the Chief instead.
- **Why:** Previous instructions were contradictory — the main skill said to auto-write Captain's Log entries, while the reference file said never to write without permission. Also, there was no clear line between what belongs in the log vs. operational databases. The new routing logic resolves both issues.

### Added four-level hierarchy: Domain > Department > Project > Task
- **Files:** skills/chief-of-staff/SKILL.md, references/notion-conventions.md, references/file-based-conventions.md
- **What:** New organizational hierarchy covering all of life — not just work. Domains (life entities like businesses, personal), Departments (functional areas within a domain), Projects (specific initiatives), Tasks (actionable to-dos). Three new databases/folders added: Domains, Departments, Tasks. Projects updated with Department relation. Briefings now include a TASKS section. Interactive mode supports task commands ("add a task", "what's on my plate", "mark X as done"). Setup asks users to define their domains and departments.
- **Why:** The system had no way to capture or track to-dos, and no department-level grouping. Research (Mikoshi Protocol) confirmed 4 levels is the cognitive sweet spot — supported by GTD, PARA, PPV, enterprise tools, and UX research. 3 levels is too flat for someone running multiple businesses plus personal life. 5+ levels creates maintenance overhead and decision fatigue.

---

## 2026-04-21

### Added Proactive Documentation
- **File:** skills/chief-of-staff/SKILL.md
- **What:** Chief now actively watches for things that should be documented — across Captain's Log, project records, State Dashboard, and watch list. Obvious updates happen automatically with an explanation; judgment calls get offered to the user.
- **Why:** Details were slipping through because documentation only happened when explicitly requested. Now the Chief catches decisions, completed tasks, status changes, and noteworthy moments across briefings, interactive sessions, and at end of session.

### Added Stay on Track check (Step 1b)
- **File:** skills/chief-of-staff/SKILL.md
- **What:** New Step 1b checks the user's calendar at session start and periodically during long sessions. If the user is in a Cowork session during a non-CoS calendar block, gives a gentle, personality-matched nudge.
- **Why:** Helps users stay aware of their schedule without being pushy — they can acknowledge and continue.
