# Product Requirements Document
## XR Global — Daily Shift Resource Planner (Web)

**Document Version:** 2.1  
**Date:** 23 June 2026  
**Prepared by:** Keith (Technical Support Manager, XR Global)  
**Status:** Released — v1 Built  
**Changelog:** v2.0 — Reflects fully built and iterated v1. F5 (Call & Chat Coverage) removed. F4 Remarks column removed. F7 split into Save Snapshot + Copy Snapshot. HC/Leave defaults set to 0. Font sizes increased across tables and labels. Numeric columns centre-aligned. Agent (Unplanned) field widened. Task list updated (TQC removed, Calls and Chats added as first task). Shift filter dropdown added to Task Assignments panel.  
v2.1 — Health banner separators changed to styled vertical bars (`│`). Platform and Team columns changed from text inputs to dropdown selects. DWE team row added to Headcount Summary. Snapshot buttons unified to dark background. Snapshot header separators added. Focus bug fixed in Agent (Unplanned) input (in-place row update). Accessibility: `role="status"` and `aria-live="polite"` added to health banner.

---

## 1. Overview

### 1.1 Background

The Global Production Support team previously managed daily shift resource planning via a shared Excel template. The spreadsheet required manual resets each day, lacked visual health indicators, and did not scale across three time-zone-separated shifts (APAC, UK, US). Screenshots shared during handovers were also difficult to read.

### 1.2 Objective

A **single-page HTML web application** that replaces the Excel workflow — faster to fill in, visually clear at a glance, and shareable without the overhead of a spreadsheet.

The tool matches the existing XR Global internal tooling design system (ZD Inspector / DESIGN.md) for visual consistency.

---

## 2. Goals

| # | Goal | Status |
|---|------|--------|
| G1 | Replace the Excel template with a clean, browser-based equivalent | ✅ Done |
| G2 | Persist daily data across sessions so the previous day serves as a baseline | ✅ Done |
| G3 | Surface coverage health at a glance per team per shift | ✅ Done |
| G4 | Enable a clean PNG export of the full planner + task panel | ✅ Done |
| G5 | Support all three shifts (APAC, UK, US) within a single page | ✅ Done |
| G6 | Allow the task assignment list to be editable (add/remove rows) | ✅ Done |

---

## 3. User

**Primary user:** Shift Lead / Support Manager (Keith)  
**Usage frequency:** Daily — once per shift transition  
**Environment:** Desktop browser (MacBook Pro M4), no backend required

---

## 4. Features

### F1 — Shift Selector Header
- Top bar with three pill tabs: **APAC | UK | US**
- Active tab: black pill (`rounded-full`) — consistent with ZD Inspector `pill-tab-active`
- Inactive tab: white background, `border-hairline`, `text-steel`
- Each shift tab stores its own:
  - **Date field** — auto-populated on page load from the machine's local clock via `new Date()`, formatted as `DD MMM YYYY` (e.g. `23 Jun 2026`); user-editable after population
  - **Shift Lead name** — free text input
- Switching tabs loads the saved state for that shift
- Top bar: `52px` height, `border-b border-hairline`, sticky

---

### F2 — Coverage Health Banner
- Full-width reactive strip rendered directly below the shift selector
- Displays: `[SHIFT] │ [DATE] │ Overall Coverage: [X%] │ Status: [HEALTHY / REDUCED / CRITICAL]`
- Segments separated by styled vertical bars (`│`, `#c8c4d0`) for clear visual chunking
- Background colour transitions in real time:
  - Green (`success-bg / success-text`) for ≥ 80% — **HEALTHY**
  - Amber (`amber-50 / amber-700`) for 50–79% — **REDUCED**
  - Red (`error-bg / error`) for < 50% — **CRITICAL**
  - Neutral (`surface / slate`) when all headcounts are 0 — **NO DATA**
- Zero-headcount teams excluded from overall calculation (no division by zero)
- `role="status"` and `aria-live="polite"` for screen reader announcements
- Signature design element — impossible to miss at a glance during handover

---

### F3 — Headcount Summary Table

**Columns per team row:**

| Field | Type | Default | Notes |
|---|---|---|---|
| Team | Label | — | L1-A5, T1-XR, L2-A5, Team Lead, Syndication, Print, DWE |
| Headcount | Number input | **0** | Persisted as baseline |
| Planned Leave | Number input | **0** | Resets to 0 on "Reset Today" |
| Unplanned Leave | Number input | **0** | Resets to 0 on "Reset Today" |
| Agent (Unplanned) | Text input | blank | Free text; clears on "Reset Today"; widened field |
| Coverage % | Auto-calculated | N/A | `(HC - Planned - Unplanned) / HC`; `N/A` if HC = 0 |
| Status | Pill badge | N/A | 🟢 Healthy ≥80% / 🟡 Reduced 50–79% / 🔴 Critical <50% / — N/A |

**Layout & behaviour:**
- Headcount, Planned Leave, and Unplanned Leave columns are **centre-aligned** (header + cell)
- Coverage % and Status update in real time on every keystroke
- Critical rows subtly tint red (`#FEF7F7`)
- Font size: 15px body, 13px column headers
- Focus ring: `border-accent ring-1 ring-accent` (#7600C4)

---

### F4 — Task Assignment Panel

**Columns:** Platform | Team | Shift | Agent Name | Task

*(Remarks column removed)*

**Pre-seeded task list (15 tasks in order):**

| # | Platform | Team | Shift | Task |
|---|---|---|---|---|
| 1 | XR | T1 | ALL | Calls and Chats |
| 2 | XR | T1 | UK | ABC News |
| 3 | XR | T1 | ALL | Advertiser Approval |
| 4 | A5 | L1 | US | SSS Password Protection |
| 5 | A5 | L1 | ALL | WB Management Mailbox |
| 6 | A5 | L1 | APAC | WB Asset Metadata Update (Run Phoenix) |
| 7 | A5 | L2 | APAC | WB Asset Metadata Update (Workflow - Zabbix) |
| 8 | A5 | L2 | APAC | WB Proxy Delete |
| 9 | A5 | L2 | US | WB Proxy Transcode Reports |
| 10 | A5 | L2 | ALL | Zabbix Monitoring |
| 11 | A5 | L2 | ALL | GDN - Delivery Monitoring |
| 12 | XR | Syndication | ALL | Monitoring XR Support Dashboard Alarm |
| 13 | XR | Syndication | ALL | News & Syndication Alarms |
| 14 | A5 | Print | ALL | Zabbix Print Monitoring |
| 15 | A5 | ALL | ALL | Prodigy AI Tools |

**Shift filter dropdown:**
- A pill-shaped `<select>` in the section header with options: APAC | UK | US
- Syncs automatically when the main shift tab is switched
- Can also be changed independently (e.g. stay on APAC headcount view while browsing UK tasks)
- Shows `X of 15 tasks` count beside the dropdown
- Always shows tasks tagged `ALL` plus those matching the selected shift

**Behaviour:**
- Platform and Team columns use dropdown selects (Platform: XR, A5; Team: T1, L1, L2, Syndication, Print, ALL)
- Shift column uses dropdown select (ALL, APAC, UK, US)
- Agent Name and Task columns are free-text inputs
- All columns are editable inline
- **Add Row** (`button-secondary` pill) at the bottom — new rows default to the active shift
- **Delete row** (×) appears on hover per row
- Pre-seeded rows: `border-l-2 border-hairline` left bar
- User-added rows: `border-l-2 border-accent` left bar (XR purple)
- Task panel spans full page width

---

### F5 — ~~Call & Chat Coverage Panel~~
**Removed.** Panel and all associated data structures have been deleted from the application.

---

### F6 — Persistence (localStorage)
- All inputs auto-save to `localStorage` on every input event — no save button
- On page load, previous session data loads as the baseline
- Each shift (APAC, UK, US) has its own isolated key namespace
- localStorage key: `xr_shift_planner_v3` (versioned; bumped on seed task list changes to force re-seed)
- **"Reset Today"** button: clears Planned Leave, Unplanned Leave, and Agent Name only — preserves Headcount baseline and task assignments
- **"Reset All"** button: clears all localStorage for the tool — requires confirmation modal before executing
- Both buttons styled as `button-secondary` / `button-danger` pills

---

### F7 — Snapshot Export
Two buttons in the top-right of the header bar, both using `html2canvas` (CDN). Both buttons share the same dark (black) pill style.

**Save Snapshot**
- Renders the full planner to a 2× retina PNG
- Downloads as: `XR_ShiftPlan_[SHIFT]_[YYYYMMDD].png`
- Action buttons, reset controls, and the shift selector bar are hidden from the export via `data-no-export`
- An export-only header strip is injected into the snapshot showing: XR logo, active shift pill, date, shift lead name, separated by styled vertical bars

**Copy Snapshot** (sits left of Save)
- Renders the same canvas as Save Snapshot
- Writes the PNG blob directly to the clipboard via `navigator.clipboard.write()`
- Allows direct paste into Teams, email, or any chat
- Requires HTTPS or localhost (Clipboard API restriction) — shows a toast error if unavailable (e.g. `file://` protocol)

**Snapshot content:**
Export-only header → Health Banner → Headcount Table → Task Assignments panel

---

## 5. Non-Functional Requirements

| # | Requirement |
|---|---|
| N1 | Single `.html` file — no backend, no build step, no npm |
| N2 | Works offline after first load (html2canvas from CDN is the only network dependency) |
| N3 | Minimum supported width: 1280px desktop |
| N4 | All data stays in-browser via localStorage — no external API calls |
| N5 | Page load under 1 second on modern hardware |
| N6 | Respects `prefers-reduced-motion` — all transitions disabled when set |

---

## 6. Design Direction

Inherits directly from the **ZD Inspector DESIGN.md** system.

### Palette

| Role | Token | Hex | Usage |
|---|---|---|---|
| Page Background | `canvas` | `#FFFFFF` | Full page background |
| Surface | `surface` | `#F5F3F8` | Cards, input backgrounds |
| Surface Soft | `surface-soft` | `#EDEAF2` | Hover states |
| Border | `hairline` | `#E2E0EA` | All card borders, input borders, dividers |
| Primary Text | `ink` | `#1A1A18` | Headings, strong labels |
| Secondary Text | `charcoal` | `#2E2E2C` | Body text, table cell values |
| Muted Text | `steel` | `#7C7C78` | Column headers, secondary labels |
| Accent | `accent` | `#7600C4` | Focus rings, active indicators, user-added rows |
| Success BG | `success-bg` | `#E6F7E8` | Healthy coverage banner + badge |
| Success Text | `success-text` | `#1A8A5C` | Healthy coverage text |
| Amber | `amber-50/700` | — | Reduced coverage banner + badge |
| Error | — | `#d45656` | Critical coverage border + text |

### Typography

| Role | Family | Size | Weight |
|---|---|---|---|
| App title | Roboto Slab | 15px | 600 |
| Section titles | Roboto Slab | 16px | 600 |
| Table column headers | Roboto | 13px | 600 uppercase |
| Table body / inputs | Roboto | 15px | 400 |
| Numeric / coverage % | Roboto Mono | 15px | 500 |
| Badges | Roboto | 13px | 600 |
| Buttons | Roboto | 13px | 600 |
| Banner shift label | Roboto Slab | 16px | 600 |
| Banner date / coverage | Roboto Mono | 15px | 500 |

### Layout Wireframe

```
┌──────────────────────────────────────────────────────────────────────┐
│  XR GLOBAL · Shift Resource Planner    [Copy Snapshot] [Save Snapshot]│  ← Top Bar (52px)
├──────────────────────────────────────────────────────────────────────┤
│  [APAC] [UK] [US]          Date: ________    Shift Lead: ___________  │  ← Shift Bar
├──────────────────────────────────────────────────────────────────────┤
│  ███████  APAC · 23 Jun 2026 · Overall Coverage: 85% · HEALTHY  ██████│  ← Health Banner
├──────────────────────────────────────────────────────────────────────┤
│  Headcount Summary                           [Reset Today] [Reset All]│
│  ┌───────────┬──────┬────────┬──────────┬──────────────────┬────┬────┐│
│  │ Team      │  HC  │ Plnnd  │ Unplnnd  │ Agent (Unplanned)│  % │ 🟢 ││
│  │           │ (ctr)│ (ctr)  │  (ctr)   │   (wide field)   │    │    ││
│  ├───────────┼──────┼────────┼──────────┼──────────────────┼────┼────┤│
│  │ L1 - A5   │  0   │   0    │    0     │                  │N/A │ —  ││
│  │ T1 - XR   │  0   │   0    │    0     │                  │N/A │ —  ││
│  │ ...       │      │        │          │                  │    │    ││
│  └───────────┴──────┴────────┴──────────┴──────────────────┴────┴────┘│
├──────────────────────────────────────────────────────────────────────┤
│  Task Assignments                    Show shift: [APAC ▾]  X of 15   │
│  ┌──┬──────────┬──────┬──────┬───────────────┬─────────────────────┐  │
│  │  │ Platform │ Team │ Shift│  Agent Name   │ Task                │  │
│  ├──┼──────────┼──────┼──────┼───────────────┼─────────────────────┤  │
│  │▌ │ XR       │ T1   │ ALL  │               │ Calls and Chats     │  │
│  │▌ │ XR       │ T1   │ UK   │               │ ABC News            │  │
│  │  │ ...      │      │      │               │                     │  │
│  └──┴──────────┴──────┴──────┴───────────────┴─────────────────────┘  │
│  [+ Add Row]                                                          │
└──────────────────────────────────────────────────────────────────────┘
```

### Signature Design Element
The **reactive health banner** — a full-width, colour-animated strip between the shift selector and the headcount table. Background colour, text, and status label all update in real time as headcount inputs change.

---

## 7. Out of Scope (v1)

- Multi-user / real-time collaboration
- Backend or cloud sync
- Historical trend charts
- Mobile/tablet layout
- PDF export
- Authentication
- Call & Chat Coverage panel (removed from v1)

---

## 8. Success Criteria

| Metric | Target | Met |
|---|---|---|
| Time to fill out daily planner | < 2 minutes | ✅ |
| Coverage health visible above the fold | Yes | ✅ |
| No data loss across browser sessions | Yes | ✅ |
| PNG export usable in Teams/email handover | Yes | ✅ |
| Copy to clipboard for instant paste | Yes | ✅ |
| No installation or build step required | Yes | ✅ |
| Visual consistency with ZD Inspector | Yes | ✅ |

---

## 9. Resolved Questions

| # | Question | Answer |
|---|---|---|
| OQ1 | Task assignment list editable in v1? | ✅ Yes — add/edit/remove rows |
| OQ2 | Can headcount be zero? | ✅ Yes — coverage shows N/A, excluded from overall % |
| OQ3 | Snapshot scope? | ✅ Full page — export header + health banner + headcount table + task panel |
| OQ4 | Call & Chat Coverage panel included? | ❌ Removed from v1 |
| OQ5 | Remarks column in Task Assignments? | ❌ Removed from v1 |
| OQ6 | Default values for HC/Leave fields? | ✅ All default to 0 |
| OQ7 | Task filter — independent or tied to shift tab? | ✅ Syncs with shift tab but independently adjustable |
| OQ8 | Snapshot behaviour? | ✅ Two actions: Save (download to disk) + Copy (to clipboard) |

---

*End of PRD v2.0*
