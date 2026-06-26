# XR Global Resource Planner — AGENTS.md

## Architecture

- **Single HTML file** — `Resource Planner.html` (not `index.html`). No build, no npm, no backend.
- Vanilla JS + CSS. No frameworks. Google Fonts (Roboto, Roboto Slab, Roboto Mono) and `html2canvas@1.4.1` loaded from CDN.
- Works offline after first load. All data in `localStorage` under key `xr_shift_planner_v3`.
- Each shift (apac, uk, us) stores isolated state: `{ date, lead, hc, leave, unplanned, agent, remark, tasks, lastTaskId }`.

## Key constants to know

- `TEAMS` = `['L1 - A5','T1 - XR','L2 - A5','Team Lead','Syndication','Print','DWE']` — add a team by appending here.
- `SEED_TASKS` — 15 pre-seeded task rows. To force re-seed, bump `STORAGE_KEY` version.
- Platform dropdown values: `XR, A5`. Team dropdown values: `T1, L1, L2, Syndication, Print, ALL`.

## Design tokens (CSS variables)

`--canvas #FFF`, `--surface #F5F3F8`, `--hairline #E2E0EA`, `--ink #1A1A18`, `--charcoal #2E2E2C`, `--steel #7C7C78`, `--accent #7600C4`, `--success-bg #E6F7E8`, `--success-text #1A8A5C`, `--error #d45656`, `--amber-bg #FFFBEB`, `--amber-text #B45309`.

## Snapshot quirks

- Elements with `data-no-export` attribute are stripped from the cloned document during snapshot rendering (using `html2canvas` `onclone` callback).
- The snapshot header (`#snapshotHeader`, `display:none` by default) is shown only in the clone.
- Snapshot filename: `XR_ShiftPlan_[SHIFT]_[YYYYMMDD].png`.
- Copy Snapshot requires HTTPS/localhost (Clipboard API restriction). Falls back to a toast message.

## Headcount table gotcha

`updateHC()` updates coverage/badge cells **in-place** via `updateRow()` — it does NOT call `renderHC()` (which destroys all inputs and loses focus). Never add a full `renderHC()` call inside keystroke handlers.

## Health banner

- Uses `role="status"` and `aria-live="polite"` for accessibility.
- Segments separated by styled vertical bars (`│`, `#c8c4d0`).
- Zero-HC teams excluded from overall coverage calculation.

## Source of truth

- `PRD_XR_Shift_Resource_Planner.md` — canonical feature spec for all requirements.
- Do not change seed data, team list, or color tokens without updating the PRD.
