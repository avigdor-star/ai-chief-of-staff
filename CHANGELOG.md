# Chief of Staff — Changelog

Changes to the skill during beta development. Newest first.

---

## 2026-04-22

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
