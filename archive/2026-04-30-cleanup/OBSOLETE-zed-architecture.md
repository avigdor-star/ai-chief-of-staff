# Zed — Full Architecture Blueprint

**Status:** Draft for review. Nothing has been built yet. This is the plan.
**Date:** 2026-04-25
**Author:** Claude (working with Avigdor)

---

## How to read this document

This is the complete blueprint for **Zed** — your new Chief of Staff skill, built from scratch with cleaner architecture. You decide what goes through to a build, what changes, what gets cut.

Read it top to bottom. Each section answers one question. At the end there's a checklist of decisions for you to mark before any file gets created.

---

## Table of contents

1. Why we're rebuilding (short recap)
2. Design principles
3. Locked decisions
4. The folder structure
5. SKILL.md blueprint
6. Flow files blueprint (the new `flows/` folder)
7. Reference files plan
8. Universal Gates (the hard rules)
9. The Outline / Routing Index — master table
10. Bootstrap sequence
11. Coexistence with old CoS while testing
12. Testing plan
13. Build order
14. What I need from you before I write a single file

---

## 1. Why we're rebuilding (short recap)

The current Chief of Staff skill works, but its main file is 763 lines and gets fully loaded every time you say "log this" or "briefing" or anything else. Setup logic that runs once in your life is loaded every session forever. Hard rules are scattered across the document. There's no routing index at the top, so the model has to read everything to figure out what to do.

The fix: a thin SKILL.md that routes, plus on-demand flow files for the heavy stuff. Same features, faster loading, cleaner thinking, easier to maintain.

Your data is unaffected. The skill is just instructions.

---

## 2. Design principles

These are the rules that shaped every decision below.

**P1. Lazy-load the heavy stuff.** Anything that runs sometimes lives in a flow file. Anything that runs every session lives in SKILL.md.

**P2. One clear job per file.** No file does more than one thing. If a file is starting to do two jobs, it gets split.

**P3. Hard gates live in one place.** Snapshots, approval rules, filter rules, Axiom checks — listed once at the top of SKILL.md as Universal Gates. Every flow points at them.

**P4. The Outline is sacred.** The routing index at the top of SKILL.md is the model's map. Maintain it religiously.

**P5. No semantic drift from the original.** Where Zed copies CoS behavior, it copies it exactly. Improvements are intentional and called out, never accidental.

**P6. Plain language for the user, technical precision for the model.** Glossary keeps user-facing wording soft. Internal flow files use whatever terms get the job done.

**P7. Stable names are sacred.** Anything Zed reads from your existing data (folder names, page names, property names) must match what's already there.

---

## 3. Locked decisions

From our prior conversation:

1. **Full feature parity with current CoS.** Nothing dropped in v1. Trim later if anything stays unused.
2. **Chief Name stays configurable.** Skill is named `zed`, but the talking name comes from `State Dashboard → My Setup → Chief Name`. If you want to call it Friday next year, you can.
3. **Same natural triggers as CoS.** "briefing," "what's going on," "catch me up," "what's on my plate," etc. — Zed responds to all the same phrases.
4. **Logging folded into Zed.** No separate `log` skill. Documentation routing lives inside Zed.
5. **References get a light tidy + one new shared file.** Snapshot/backup duplication consolidated into `vault-safety.md`.

---

## 4. The folder structure

```
zed/
├── SKILL.md                            ← Thin router, ~250-350 lines
├── flows/                              ← New: on-demand flow files
│   ├── setup.md                        ← First-time setup (runs once)
│   ├── briefing.md                     ← Mode A: briefings
│   ├── interactive.md                  ← Mode B: dispatcher for non-briefing requests
│   ├── documentation-routing.md        ← log this / captain's log / journal routing
│   ├── task-management.md              ← Add task, mark done, what's blocked, move task
│   ├── file-cabinet.md                 ← Smart nesting, dangling records, structure cleanup
│   ├── watcher-setup.md                ← Set up watchers, express lane
│   ├── captains-log-recall.md          ← "What did I log about X" queries
│   └── alerts.md                       ← Step 3 alert cascade
└── references/                         ← Mostly verbatim from CoS
    ├── personality.md                  ← Verbatim copy
    ├── signal-filters.md               ← Verbatim copy
    ├── platform-choice.md              ← Verbatim copy
    ├── captains-log.md                 ← Verbatim copy
    ├── watcher-playbook.md             ← Verbatim + 1 cross-pointer added
    ├── rollout-reminder.md             ← Verbatim + 1 cross-pointer added
    ├── file-based-conventions.md       ← Tidy: snapshot section moves to vault-safety
    ├── notion-conventions.md           ← Tidy: drop preamble + snapshot section
    ├── backup-setup.md                 ← Tidy: drop "what is backup" intro
    └── vault-safety.md                 ← NEW: snapshot vs backup, snapshot rules, restoration
```

**File count:** 1 SKILL.md + 9 flow files + 10 reference files = **20 files**.

Compared to CoS today: 1 SKILL.md + 9 reference files = 10 files. Doubled, yes — but each is smaller and only loaded when needed.

---

## 5. SKILL.md blueprint

The router. ~250-350 lines. Every line earns its place.

**Section order:**

```
[1] YAML frontmatter (name, description with all triggers)
[2] Changelog
[3] Preface (5-10 lines: who Zed is, the one routing rule)
[4] The Outline / Routing Index (the master table — see Section 9)
[5] Bootstrap (Step 0 + identity load + axiom checks — always runs)
[6] Universal Gates (the hard rules — see Section 8)
[7] Decision Rules (never act without approval, etc.)
[8] Trusted Actions (what Zed can do without asking)
[9] Glossary (user-facing wording)
[10] Stable Names (verbatim copy from CoS — DO NOT TOUCH)
[11] Reference Manifest (what's in references/ and flows/)
```

**What's NOT in SKILL.md:**
- Setup walkthrough (lives in `flows/setup.md`)
- Briefing flow (lives in `flows/briefing.md`)
- Interactive sub-flows (live in `flows/`)
- Alert cascade (lives in `flows/alerts.md`)
- Reference content (lives in `references/`)

**Why this works:** The model loads SKILL.md, runs Bootstrap, looks at the Outline, jumps to the right flow. Most sessions never touch most flow files.

---

## 6. Flow files blueprint

Each flow file is self-contained, has a clear scope, and starts with three lines:

```
**What this flow does:** [one line]
**When to load it:** [one line]
**Prerequisites:** [Bootstrap complete; any other gates listed here]
```

### 6.1 `flows/setup.md`

**What it does:** First-time setup walkthrough — creates the entire Chief of Staff structure, populates State Dashboard, runs connector tests, sets up Captain's Log, prompts for encrypted backup.

**When to load it:** SKILL.md Bootstrap fails to find a Chief of Staff location, OR State Dashboard exists but `setup_status` is `incomplete`, OR the user explicitly says "run setup."

**Contents:** All of CoS's current Step S (S1 through S7). Resume-from-partial-setup logic. Missing-index fallback. Email send test. Captain's Log creation. Encrypted backup prompt.

**Estimated size:** ~250 lines.

**Loading frequency:** Once per user, ever.

### 6.2 `flows/briefing.md`

**What it does:** Mode A — runs a briefing. Section-by-section delivery, tailored questions, queued actions, housekeeping at the end.

**When to load it:** User says "briefing," "what's going on," "catch me up," "morning update," "end of day," or a scheduled briefing run fires.

**Contents:**
- Source scan (calendar, email, tasks, messaging, life context)
- Filter Rules hard gate
- 12 briefing sections in order: Alerts → Carryover → Schedule → Top 3 → Tasks → Messages → Missed Message Check → Watch List → Risks/Blockers → Recommended Actions → File Cabinet Check → Rollout Nudge
- Tailored question after each section
- Mid-section action queueing
- Post-briefing housekeeping (snapshot → save briefing → update State Dashboard → session anchor → sync messaging)

**Estimated size:** ~180 lines.

**Loading frequency:** Daily-to-multiple-daily.

### 6.3 `flows/interactive.md`

**What it does:** Mode B dispatcher. Picks which interactive sub-flow handles the request.

**When to load it:** Any user request that isn't a briefing, documentation, or first-time setup.

**Contents:**
- Quick task dispatch rules (handle directly / hand to background helper / suggest fresh Cowork session)
- Sub-flow routing table:
  - Add/manage task → `flows/task-management.md`
  - Smart nesting / dangling records → `flows/file-cabinet.md`
  - Watcher setup → `flows/watcher-setup.md`
  - Captain's Log recall → `flows/captains-log-recall.md`
  - Status questions, watch list adds, priority changes, missed message reports, connector updates → handled inline (these are short)
- Proactive documentation rules (operational auto-apply; Captain's Log moments → ask first)
- End-of-session documentation sweep

**Estimated size:** ~120 lines.

**Loading frequency:** Most non-briefing sessions.

### 6.4 `flows/documentation-routing.md`

**What it does:** Routes "log this," "document this," "captain's log," "remember this," "journal this," etc. to the right destination.

**When to load it:** User says any quick-documentation phrase — even at the start of a fresh session.

**Contents:**
- Strict routing order: operational first → ask if unsure → Captain's Log only as explicit fallback
- Explicit Captain's Log phrase shortcut (bypass routing, write directly per `references/captains-log.md`)
- Captain's Log inference rules (Type, Project)

**Estimated size:** ~80 lines.

**Loading frequency:** Whenever the user logs something.

### 6.5 `flows/task-management.md`

**What it does:** Add tasks, mark done, query tasks, move between projects, query blocked items.

**When to load it:** "Add a task," "remind me to," "I need to," "mark X done," "what's on my plate," "what's blocked," "move task to project."

**Contents:**
- Task creation (infer project, priority, due date; tell user what was picked)
- Task status updates
- Task queries (group by project, sort by due date)
- Project assignment changes
- Hand-off to file-cabinet flow if a task references a non-existent project

**Estimated size:** ~70 lines.

**Loading frequency:** Multiple times per active session.

### 6.6 `flows/file-cabinet.md`

**What it does:** Smart nesting helper. Infers Domain/Department/Project/Task level from user language, offers to nest properly.

**When to load it:** User mentions a new task/project/department/domain that isn't already nested correctly. Also loaded by `briefing.md` for the File Cabinet Check section.

**Contents:**
- Four-level hierarchy explainer (Domain > Department > Project > Task)
- Inference rules from user language
- Offer-to-nest pattern (don't force)
- Dangling record handling (acceptable state, surfaces as nudge in briefings)

**Estimated size:** ~60 lines.

**Loading frequency:** Occasional.

### 6.7 `flows/watcher-setup.md`

**What it does:** Walks the user through phased rollout of automatic background scans.

**When to load it:** "Set up my watchers," "install Phase 1," "express lane," "do them all."

**Contents:**
- Disambiguation: one-at-a-time vs Express Lane
- Express Lane warning script
- Per-watcher install logic (calls scheduled task tool directly)
- Loads `references/watcher-playbook.md` and `references/rollout-reminder.md` for full specs

**Estimated size:** ~50 lines.

**Loading frequency:** Once during onboarding, occasionally for adjustments.

### 6.8 `flows/captains-log-recall.md`

**What it does:** Query the Captain's Log for entries matching user requests.

**When to load it:** "What did I log about X," "show me my wins this month," "what did I decide about Y."

**Contents:**
- Query patterns (by Type, by Project, by date range, by keyword)
- Result formatting (Title + Date + Type + one-line snippet)
- Loads `references/captains-log.md` for schema details

**Estimated size:** ~30 lines.

**Loading frequency:** Occasional.

### 6.9 `flows/alerts.md`

**What it does:** Step 3 alert cascade — sends out-of-band alerts when conditions match.

**When to load it:** Auto-triggered by alert conditions detected during briefing or interactive flows. Not user-invoked.

**Contents:**
- Alert trigger conditions (24h reply waits, 12h hard deadlines, high-priority person, payment/billing, connector failure)
- 3-tier cascade (email → messaging DM → Live Feed)
- Email-skip logic when send test failed
- Alert threshold respect (Only emergencies / Important / Everything notable)

**Estimated size:** ~50 lines.

**Loading frequency:** When alerts fire.

---

## 7. Reference files plan

| File | Status | Action |
|------|--------|--------|
| `personality.md` | Verbatim copy | Just copy it across |
| `signal-filters.md` | Verbatim copy | Just copy it across |
| `platform-choice.md` | Verbatim copy | Just copy it across |
| `captains-log.md` | Verbatim copy | Just copy it across |
| `watcher-playbook.md` | Verbatim + 1 line | Add "See `rollout-reminder.md` Section E for Express Lane validation hand-off" |
| `rollout-reminder.md` | Verbatim + 1 line | Add "See `watcher-playbook.md` Validation Checklist for what gets validated" |
| `file-based-conventions.md` | Tidy | Remove snapshot rules section; replace with one-liner pointer to `vault-safety.md` |
| `notion-conventions.md` | Tidy | Remove preamble (duplicates file-based-conventions) and snapshot section; replace with one-liner pointer to `vault-safety.md` |
| `backup-setup.md` | Tidy | Remove "what encrypted backup means" intro; replace with one-liner pointer to `vault-safety.md` |
| `vault-safety.md` | **NEW** | Consolidates: snapshot vs backup definitions, snapshot rules, snapshot restoration (both platforms), when-to-snapshot rules |

**Net change:** ~150 lines of duplication removed across 3 existing files, ~80 lines added in the new `vault-safety.md`. Net reduction in references/ folder: ~70 lines.

---

## 8. Universal Gates (the hard rules)

These live as a section in SKILL.md. Every flow file checks them before acting. Listed once, never repeated.

**G1. Approval gate.** Never take action without approval, except alerts (which are auto-fire by definition).

**G2. Snapshot gate.** Before updating the State Dashboard, snapshot first. If snapshot fails, STOP — surface the failure, do not update.

**G3. Filter Rules gate.** Before delivering ANY item to the user (in any briefing section, any recommendation, any surfaced message), check it against `State Dashboard → Filter Rules`. Match by sender, domain, company, or topic → drop the item silently.

**G4. Axiom 1 — Backup check.** At session start, check `State Dashboard → System Health → Connectors → Backup`. If status is `NOT_CONFIGURED`, `CONFIGURED_UNVERIFIED`, or `STALE` (older than the platform's threshold: 10 days for Notion, 30 days for file-based) → surface at the top of the next briefing. "Later" is allowed but the flag repeats every session until resolved.

**G5. Axiom 2 — Structure check.** At session start, scan for files/records that violate conventions. Surface as "Structure Drift: [N] items" with first 10 listed. Never silently fix — always ask.

**G6. Trusted actions list.** Specific actions Zed CAN do without per-action approval (snapshot+update State Dashboard, save briefings, archive old briefings, clear handled Live Feed items, send alert emails/DMs, post messaging summaries, sync messaging→Live Feed, respond to check-in questions). All other actions require explicit approval.

**G7. Stable Names rule.** Never rename a tracked structure (folder, database, page, property) without a migration step that handles old names first. (Full list in the Stable Names section.)

**G8. Plain language rule.** When talking to the user, use Glossary terms. Internal terms (watcher, sub-agent, connector, etc.) stay in flow files but get translated when surfacing to the user.

---

## 9. The Outline / Routing Index (master table)

This is the most important table in SKILL.md. The model uses it to decide which flow to load. Here's the planned content:

| If the user… | Load |
|--------------|------|
| Says "briefing," "what's going on," "catch me up," "morning update," "end of day," "what should I focus on," "what's urgent," "did I miss anything" | `flows/briefing.md` |
| Says "log this," "document this," "record this," "note this down," "remember this," "capture this," "captain's log," "journal this/that" | `flows/documentation-routing.md` |
| Says "add a task," "remind me to," "I need to," "mark X as done," "I finished," "what's on my plate," "what tasks," "what's blocked," "move [task] to" | `flows/task-management.md` |
| Mentions a new domain, department, project, or task that needs nesting | `flows/file-cabinet.md` |
| Says "set up my watchers," "install Phase 1," "express lane," "do them all" | `flows/watcher-setup.md` |
| Asks "what did I log about X," "show me my wins," "what did I decide about Y," any Captain's Log query | `flows/captains-log-recall.md` |
| Has no Chief of Staff location yet, OR setup_status is incomplete | `flows/setup.md` |
| Says anything else (research request, draft, status question, watch list, priority change, missed message, connector update) | `flows/interactive.md` |
| (Auto-triggered when alert conditions match) | `flows/alerts.md` |

**Routing rule at the top of SKILL.md:** "Run Bootstrap. Then read the Outline. Load only the flow it points to. Do not read other flows unless that flow points you to one."

---

## 10. Bootstrap sequence

Lives in SKILL.md. Runs at the start of every session. ~50-70 lines.

**Steps:**

1. **Find Chief of Staff location.** Look for local `Chief-of-Staff/` folder first. If not found, check Notion. If neither exists → load `flows/setup.md`.

2. **Load identity.** Read State Dashboard's `My Setup` section: Name, Email, Role, Company, Chief Name, Personality, Platform, alert threshold, setup_status.

3. **Check setup_status.** If `incomplete` or has placeholder values → load `flows/setup.md` to resume.

4. **Load personality.** Read `references/personality.md` for tone rules matching `My Setup → Personality`.

5. **Run Axiom 1** (silently). If backup status flags → queue for surfacing in next user-facing output.

6. **Run Axiom 2** (silently). If structure drift detected → queue for surfacing.

7. **Run Stay-on-Track check.** Look at calendar block right now. If user is mid-meeting/mid-task that isn't CoS-related, gently nudge.

8. **Read the Outline.** Match user request to the right flow file. Load it. Run it.

That's it. Bootstrap is short, predictable, and the same every session.

---

## 11. Coexistence with old CoS while testing

You'll have both skills installed during the test period. Here's how to keep them from fighting:

**Option A — Pause CoS while you test Zed.** Easiest. In Claude's plugin manager, deactivate CoS. Activate Zed. Use Zed exclusively for a week. If you like it, leave CoS deactivated permanently or uninstall.

**Option B — Run both with different triggers.** Zed gets its natural triggers. CoS gets all its natural triggers stripped out and replaced with one explicit phrase like "old cos." Risk: if you forget which is which, you'll get confused.

**My recommendation: A.** Cleaner test, simpler mental model.

**Either way, your data is safe.** Both skills read the same State Dashboard, Captain's Log, etc. Switching between them is just switching brains, not data.

---

## 12. Testing plan

After Zed is built, test these in order. Each is a real-world scenario, not a synthetic one.

**T1. Cold start, basic briefing.** Trigger: "briefing." Expected: Zed runs Bootstrap, loads `flows/briefing.md`, delivers section-by-section briefing. Pass criteria: same content quality as CoS would deliver.

**T2. Quick log.** Trigger: "log: had a great call with Sarah." Expected: Zed routes to operational first (any matching project/task?), asks if unsure. Pass criteria: doesn't auto-default to Captain's Log.

**T3. Add a task.** Trigger: "add a task: review Q2 budget by Friday." Expected: task gets created, project inferred, due date inferred, summary back to user. Pass criteria: created in correct database with right metadata.

**T4. Captain's Log recall.** Trigger: "what did I log about [topic]." Expected: Zed queries Captain's Log, returns matches. Pass criteria: results match what's actually there.

**T5. Watch list add.** Trigger: "keep an eye on [thing]." Expected: added to State Dashboard Watch List. Pass criteria: shows up in next briefing.

**T6. Setup-aware behavior.** With a fresh test vault (no setup yet), trigger Zed. Expected: Bootstrap detects no CoS location, loads `flows/setup.md`, walks the full setup. Pass criteria: setup completes end-to-end on file-based platform AND Notion.

**T7. Alert handling.** Manually create a condition that should fire an alert (overdue payment-related task, e.g.). Expected: 3-tier cascade triggers. Pass criteria: email arrives or is drafted, message posts, Live Feed gets entry.

**T8. Hard gates.** Try to trigger a State Dashboard update with simulated snapshot failure. Expected: Zed stops, surfaces the error. Pass criteria: no update applied.

**T9. Filter Rules gate.** Add a filter rule. Trigger a briefing where the filtered item would normally appear. Pass criteria: the filtered item does NOT appear.

**T10. Personality match.** Set personality to "Playful & lighthearted." Trigger any flow. Pass criteria: Zed's tone matches the personality reference.

**Pass threshold:** all 10 tests pass with no behavior regressions vs CoS. Any regression → fix before declaring Zed ready.

---

## 13. Build order

The order I'd build in. Each step ends in a checkpoint where you can review.

**B1. Scaffolding.** Create the folder structure (empty files with their headers). You see the shape before any content.

**B2. SKILL.md draft.** Write the router. Preface, Outline, Bootstrap, Universal Gates, Glossary, Stable Names, Reference Manifest. You read it end-to-end. Approve or revise.

**B3. References — verbatim copies.** Copy the 4 reference files that don't change (`personality`, `signal-filters`, `platform-choice`, `captains-log`). Quick.

**B4. References — `vault-safety.md` new.** Write the new shared file. Test by reading: does it cover all three duplicate definitions cleanly?

**B5. References — tidy 3 existing files.** Trim `file-based-conventions`, `notion-conventions`, `backup-setup`. Replace duplicated sections with pointers to `vault-safety.md`.

**B6. References — add cross-pointers.** Update `watcher-playbook` and `rollout-reminder` with the explicit cross-references identified in the audit.

**B7. Flow files — Bootstrap-adjacent.** Write `flows/setup.md` first since it's the biggest. Test the setup flow end-to-end.

**B8. Flow files — core daily flows.** Write `flows/briefing.md`, then `flows/interactive.md`. Test against your actual usage.

**B9. Flow files — sub-flows.** Write `flows/documentation-routing.md`, `flows/task-management.md`, `flows/file-cabinet.md`. Test.

**B10. Flow files — utility.** Write `flows/watcher-setup.md`, `flows/captains-log-recall.md`, `flows/alerts.md`. Test.

**B11. Run T1-T10 testing plan.** Real-world tests in order.

**B12. Decide.** If Zed wins, deactivate or uninstall old CoS. Done.

---

## 14. What I need from you before I write a single file

Mark each one ✅ approve, ❌ reject, or ✏️ revise:

| # | Decision | Your call |
|---|----------|-----------|
| 1 | **Folder structure** as laid out in Section 4 (1 SKILL.md + 9 flows + 10 references) | |
| 2 | **SKILL.md sections** as laid out in Section 5 | |
| 3 | **9 flow files** as scoped in Section 6 | |
| 4 | **Reference plan** as laid out in Section 7 (4 verbatim, 2 add cross-pointer, 3 tidy, 1 new) | |
| 5 | **Universal Gates** G1-G8 as laid out in Section 8 | |
| 6 | **Outline / Routing Index** as laid out in Section 9 | |
| 7 | **Bootstrap sequence** as laid out in Section 10 | |
| 8 | **Coexistence approach:** pause CoS during testing (Option A) | |
| 9 | **10-test testing plan** in Section 12 | |
| 10 | **Build order** B1-B12 in Section 13 | |

**Anything you want to change?** Tell me what and we revise the doc before any build starts.

**Anything you want to add?** Same — tell me, doc gets revised first.

When all 10 are ✅, we begin with B1 (scaffolding) and you approve at each checkpoint.

---

## Appendix — risks I'm watching

These are the failure modes Mikoshi flagged. Each has a mitigation built into the plan.

| # | Risk | Where mitigated |
|---|------|-----------------|
| 1 | Capability gap (Zed misses a CoS feature) | Sections 6, 9, 12 — feature inventory + 10-test plan |
| 2 | Data path mismatch | Section 8 G7 — Stable Names rule |
| 3 | Schema drift | Section 8 G7 — Stable Names rule + verbatim reference copies |
| 4 | Trigger collision during test | Section 11 — pause CoS |
| 5 | Reference divergence | Section 7 — verbatim copies for stable files |
| 6 | Hard gates skipped | Section 8 — Universal Gates listed once, every flow points there |
| 7 | Stable Names damage | Section 5 — copied verbatim into SKILL.md, flagged DO NOT TOUCH |
| 8 | Setup parity loss | Section 6.1 — full S1-S7 with all guards intact |
| 9 | Existing artifacts ignored | Section 8 G7 + Section 11 — Zed reads existing data as canon |
| 10 | Chief Name handling broken | Section 3 decision 2 — configurable, read from State Dashboard |

---

**End of architecture document.** Awaiting your decisions on Section 14.
