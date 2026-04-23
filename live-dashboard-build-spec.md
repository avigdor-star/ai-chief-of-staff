# Chief of Staff Live Dashboard — Build Spec

Hand this to a fresh Claude session to build the live artifact. Do the probe step first. Do not skip it.

## Your task

Build a Cowork live artifact that serves as a single-page dashboard for a Chief of Staff Notion workspace. The artifact is generated per-user against their actual Notion page IDs and saved to the sidebar so it persists across sessions.

## Context you need

The Chief of Staff plugin is a Claude Code / Cowork plugin. Its core skill reads and writes to a structured Notion workspace: a parent page called "Chief of Staff" with child databases (Tasks, Projects, Briefings, Captain's Log, Domains, Departments, People) and a State Dashboard page that holds user preferences, Watch List, and Open Decisions.

A "live artifact" in Cowork is a self-contained HTML page with inline JS that calls `window.cowork.callMcpTool()` on load to fetch fresh data from the user's connectors. It persists across sessions and refreshes on open.

## Layout — three zones

### Zone 1 (top, primary): "What's Changed Since Your Last Briefing"

This is the hero. Not a rehash of the briefing. Show deltas against the latest briefing's timestamp:

- Tasks completed since the briefing
- Tasks added since the briefing
- New Open Decisions opened since the briefing
- Watch List items that moved (status change, new activity)
- New Captain's Log entries since the briefing

If there is no prior briefing, render the first-time empty state (see below).

### Zone 2 (middle): "Live Right Now"

Two sections only:

1. **Tasks** — from Tasks DB. Status in {to-do, in-progress, blocked}. Grouped: Overdue / Due Today / Upcoming / Blocked. Show Name, Project (via relation), Due Date, Priority. Each item links to its Notion page.
2. **Watch List + Open Decisions** — from the State Dashboard page. Title plus a one-line summary per item. Links to source.

No "Active Projects" section. No "Captain's Log last 7 days" section. Cut from v1.

### Zone 3 (bottom, collapsed by default): "This Morning's Briefing"

The latest briefing narrative from the Briefings DB, collapsed with an expand toggle. Render in the section order the skill already uses (Carryover, Top 3, Schedule Snapshot, Tasks, Messages, Watch List, Missed Messages, Risks, Recommended Actions, Recent Log, Rollout Nudge).

### Header

- Title: the user's custom Chief name from State Dashboard -> My Setup -> Chief Name. Fall back to "Chief of Staff" if unset.
- "Last refreshed at HH:MM" timestamp.
- No custom refresh button. Cowork's artifact header already has one.

## Out of scope for v1

- Action buttons. Read-only. Click through to Notion to edit.
- Re-generating the briefing from the dashboard (still happens in chat).
- Charts, graphs, trend lines.
- Mobile-specific layout beyond basic responsive behavior.
- Multi-user or team features.
- Caching.
- File-based platforms. Notion only in v1.

## Platform gate

Before doing anything, read State Dashboard -> Platform. If it is not `notion`, stop and return this message:

> "The live dashboard is Notion-only in v1. Your platform is set to {platform}. File-based dashboards are on the roadmap."

Do not create an artifact in that case.

## Step 1 — Build-time probe (mandatory)

Before writing any HTML, confirm:

1. The "Chief of Staff" parent page exists. Capture its page ID.
2. These child databases exist: Tasks, Projects, Briefings, Captain's Log, Domains, Departments, People. Capture each ID.
3. The State Dashboard page exists. Capture its ID.
4. The latest briefing (if any) can be fetched from the Briefings DB.
5. The exact property names on Tasks: Status, Due Date, Priority, Project relation. Capture them in a `schema` object.
6. The exact property names on Briefings and Captain's Log used by Zone 1 queries.
7. The State Dashboard page contains Watch List and Open Decisions sections in a parseable form.

If any check fails, stop and report back. Do not build a half-broken artifact.

## Step 2 — Bake IDs and schema into the HTML

Inline a config object at the top of the generated HTML:

```js
const CONFIG = {
  parentPageId: "...",
  databases: {
    tasks: "...", projects: "...", briefings: "...",
    captainsLog: "...", domains: "...", departments: "...", people: "..."
  },
  stateDashboardPageId: "...",
  schema: {
    tasks: { status: "Status", dueDate: "Due Date", priority: "Priority", project: "Project" },
    briefings: { date: "Date", ... },
    captainsLog: { date: "Date", type: "Type", title: "Title" }
  },
  chiefName: "...",
  personality: "Professional" // or Playful / Dry Wit / Warm
};
```

Do not re-search for pages or databases at runtime. Baked IDs are fast and robust against renames.

## Step 3 — Runtime data fetches

On page load, call `window.cowork.callMcpTool()` to run, in parallel where possible:

- Latest briefing from Briefings DB (sort Date desc, limit 1). Extract timestamp.
- Tasks filtered by Status in {to-do, in-progress, blocked}.
- State Dashboard page content; parse Watch List and Open Decisions.
- For Zone 1: tasks completed since briefing timestamp, tasks created since briefing timestamp, Open Decisions opened since briefing timestamp, Captain's Log entries since briefing timestamp.

## Step 4 — Rendering rules

- Inline CSS with CSS variables. Light color scheme per Cowork artifact conventions.
- Transparent background. No top-level padding.
- Every Notion item links to its source page.
- Convert Notion property objects via small render helpers. If a property is missing or null, render "—" and keep going. Do not crash a section.
- Schema drift banner: if more than 30% of expected properties across all queries are missing, render a top-of-page banner: "Schema changed since this dashboard was built. Run /build-dashboard to rebuild."

## Step 5 — Personality in empty states

Personality does not theme the layout. It only leaks into empty-state strings. Keep a flat lookup table in the generated HTML: four personalities (Professional, Playful, Dry Wit, Warm) x each empty-state slot x three string options. Pick randomly on load so the tone has variety without being noisy.

Empty-state slots you need:

- Zone 1: no changes since briefing.
- Zone 2 Tasks: no open tasks.
- Zone 2 Watch List + Decisions: nothing on the watch list, no open decisions.
- Zone 3: no briefing yet.

Example — "no open tasks" strings:

- Professional: "No open tasks. Inbox is clear."
- Playful: "Zero tasks. Go touch grass."
- Dry Wit: "No tasks. Suspicious."
- Warm: "Nothing due. Enjoy the quiet."

## Step 6 — Error handling

- Per-section error banner if a query fails. Other sections keep rendering.
- If `window.cowork.callMcpTool` is unavailable or Notion is disconnected, render a single full-page state: "Reconnect Notion in Cowork settings to see your dashboard."

## Step 7 — First-time empty state (no prior briefing)

Zone 1 shows: "Run your first briefing to populate this section." Zone 2 and Zone 3 render normally if their data exists. Do not block the whole dashboard on a missing briefing.

## Step 8 — Save

Save via `mcp__cowork__create_artifact`. Title: `chief-of-staff-dashboard`.

After saving, tell the user:

> "Your dashboard is in the sidebar. Re-run /build-dashboard any time you want to refresh the layout, pick up schema changes, or change your Chief name or personality."

## Trigger surface

Two paths route to the same flow:

- Slash command: `/build-dashboard`
- Natural-language triggers in chief-of-staff skill: "build my dashboard", "make me a live dashboard", "set up my dashboard", "rebuild my dashboard", "refresh my dashboard layout".

## Upgrade path

Re-running `/build-dashboard` regenerates the artifact in place — new schema snapshot, latest layout, latest personality. This is how users pick up plugin updates and how they recover from property renames.

## Pre-ship test (must pass before shipping)

Cold-install test: another person installs the plugin, connects Notion with a Chief of Staff workspace containing at least one briefing, and runs `/build-dashboard`. They should see all three zones populated in under 10 minutes with zero help from me. If that fails, do not ship.

## What the plugin ships

- New command: `/build-dashboard`.
- New reference file: `references/live-dashboard.md`. Contains the HTML template, the Notion query list, the schema snapshot protocol, the empty-state string tables by personality, and the error-handling rules.
- Short addition to SKILL.md under "Interactive Mode" listing trigger phrases and pointing to the reference file.
- README note: "Live dashboard is Notion-only in v1. File-based platforms on the roadmap."

No new MCP servers. No new dependencies. No new settings.

## Known risks, documented for the README

- Notion only in v1.
- Read-only — click through to Notion to edit.
- Parses the latest briefing's structure. If you change the briefing template, re-run /build-dashboard.
- If you rename a Chief of Staff database or core property, re-run /build-dashboard.
- Assumes exactly one parent page named "Chief of Staff." Page ID is baked at build time, so duplicates won't confuse the artifact after it's built.

## Success criteria

- Pre-ship: another user completes cold install -> working dashboard in under 10 minutes without help.
- Post-ship usage: I open the dashboard at least once a day for two weeks without being reminded.
- The question "what's on my plate today" is answerable in under 3 seconds of looking at the page.

End of spec.
