---
project: EctoboxIgnitionModules / Ectobox Schedule
status: viability-verified
concept: component-comparison
technology: [ignition-perspective, kanoa-mes]
audience: internal-decision
---

# Ectobox Schedule vs Ignition Equipment Schedule

A decision-support comparison for replacing Ignition's built-in **Equipment Schedule**
(`ia.display.equipmentschedule`) with the **Ectobox Schedule Timeline**
(`ectobox.schedule.timeline`) on Kanoa scheduling screens.

Grounded in a live viability test run on the ProveIt gateway (kanoaMES) on 2026-07-27:
a new screen duplicating Balchem's `operationsGantt`, with the Ignition component swapped for
ours, fed by the same Kanoa data path. Reachable at **Ops > Scheduling > Gantt (Ectobox)**.

## Bottom line

The swap is viable and low-effort. Our component consumes the exact same Kanoa schedule data,
persists edits through the same `system.kanoa.schedule.updateScheduleBlock` call, and adds
capabilities the built-in component lacks (native cross-lane drag, accept rules, an unscheduled
tray, state/shift bands, data-integrity flagging, timezone-correct rendering, vertical mode).

The two real adoption costs are: **support ownership moves to Ectobox** (versus IA-supported),
and **recurring schedule blocks (`rruleStr`) are not yet a first-class concept** in our component.
Neither blocks a viability decision; both belong in the go/no-go discussion.

## What was verified live

| Item | Evidence |
|---|---|
| Module installed | `com.ectobox.schedule` registered on the ProveIt gateway |
| Same data source | `getData()` builds lanes from `system.kanoa.asset.getAssets` and cards from `system.kanoa.schedule.getCalendarEvents` (the identical feed the Equipment Schedule uses) |
| Renders real data | 64 live schedule blocks across 2 lanes (Enterprise B / fillerproduction, fillingline01/02), Feb 2026, with per-mode colors, work-order badges, and left-block icons |
| Persistence round-trip | Block 1448 moved +30 min via `updateScheduleBlock` (result=1, confirmed in DB), then reverted cleanly. This is the exact call the move/resize handlers run |
| Kanoa-native nav | Registered with `system.kanoa.config.addNavigationItem` (not page-config) |

Not yet tested: a live drag/resize gesture through the UI. The remote preview tooling has no drag
gesture, so `onEventMoved`/`onEventResized` firing was validated by exercising their write path
directly rather than by dragging. A drag test in Designer or a normal browser closes this.

## Data model mapping

The swap is mechanical because both components model the same thing (rows of time-blocks).

| Concept | Ignition Equipment Schedule | Ectobox Schedule Timeline |
|---|---|---|
| Rows | `items` = `{id, label, iconConfig, rowStyle, data{assetId, eqPath}}` | `lanes` = `{id, label, type, color, icon, group, accept}` |
| Blocks | `scheduledEvents` = `{itemId, eventId, startDate, endDate, label, percentDone, style{backgroundColor}}` | `events` = `{id, laneId, name, start, end, type, color, badges[], leftBlock, progress}` |
| Row link | `event.itemId` -> synthetic row id, `data.assetId` holds the real asset | `event.laneId` -> `lane.id`; use `str(assetId)` directly as the lane id (no synthetic index) |
| Times | date strings `yyyy-MM-dd HH:mm:ss Z` | ISO string or epoch millis; feed `startDate.getTime()` and set `timeZone` to the plant zone |
| Downtime | `downtimeEvents` (underlays) | `laneStates` (per-lane spans) + `globalBands` (plant-wide shifts/breaks) |

`getData()` barely changes: the same two Kanoa calls, then build our lane/event dicts instead of
theirs. Keep the full block in a lookup keyed by `scheduleBlockId` for the write-back.

## Interaction and capability delta

| Capability | Equipment Schedule | Ectobox Schedule |
|---|---|---|
| Move within a row | Yes | Yes |
| Move **between** rows by drag | **No** (Balchem worked around it with click-then-pick-target) | **Native** (`onEventMoved` carries `fromLaneId`/`toLaneId`) |
| Resize | Yes | Yes |
| Declarative move rules | No | `accept.types` / `eventIds` / `denyTypes` per lane; blocked moves fire `onMoveRejected` |
| Unscheduled backlog | No | Built-in tray with search + dev filter; drag-to-schedule-in-place |
| Drop-to-create from a palette | No | `Schedule Drag Source` companion component |
| Right-click menu | No | Configurable `contextMenu` + `onContextMenuAction` |
| State / shift bands | Downtime underlays only | `laneStates` + `globalBands`, either can block drops |
| Data-integrity flagging | Silent | Bad/duplicate/unknown-lane cards flagged and reported via `onDataError` |
| Orientation | Horizontal | Horizontal or vertical (calendar-style) |
| Timezone | Session/browser | IANA `timeZone` prop, so every operator sees the same clock |
| Theming | `headerStyles` / `rowStyle` | Auto light/dark, curated palette, CSS custom properties, prefixed classes |
| Editing UI | Unopinionated (build your own popup) | Unopinionated (build your own popup); `onEventDoubleClicked` |
| Events exposed | 4 (`onMoveEvent`, `onResizeEvent`, `onClickEvent`, `onAddEvent`) | 13 (adds dropped, deleted, unscheduled, range-changed, selection-changed, move-rejected, data-error, context-menu) |

## Persistence

Identical model. Both components are optimistic: they update their own block prop immediately and
fire a matching event. You persist on that event with `system.kanoa.schedule.updateScheduleBlock`
(and `addScheduleBlock` / `deleteScheduleBlock`), then re-read. The move/resize write path was
verified round-trip against a real block.

Our component splits what Ignition merged: `onEventMoved` carries both lanes, so a cross-asset move
and a time move are the same handler. That removes Balchem's click-to-move workaround entirely.

## Adoption effort

Low. Per screen: swap one component, adjust `getData()` to emit lane/event dicts, and rewrite the
four IA event handlers as two or three of ours (move/resize -> persist, optional double-click ->
editor). Roughly a day per screen, less once the mapping is a shared library function.

## Risks and open questions for the go/no-go

1. **Support ownership.** The Equipment Schedule is Inductive-Automation-supported; ours is
   Ectobox-maintained. Adopting it moves the upgrade and bug-fix burden to us.
2. **Recurring blocks.** Kanoa schedule blocks carry `rruleStr` and the Equipment Schedule handled
   recurrence via schedule-block exceptions. The Ectobox component has no recurrence concept in its
   current schema. Recurring schedules would need to be expanded to concrete cards before binding,
   or recurrence support added to the module.
3. **Version floor.** Ectobox Schedule is Ignition 8.3 only.
4. **Live drag.** Confirm the UI drag/resize gesture end-to-end in Designer or a browser (the one
   item the remote test could not exercise).
5. **Lead time.** The Equipment Schedule had a `leadTime` bar per block. Our component has no direct
   equivalent; `laneStates`/`globalBands` cover setup/changeover differently. Decide whether lead
   time matters for the target screens.

## Test screen reference

- View: `kanoaMES` project, `viability/schedulerGanttEcto`
- Nav: Ops > Scheduling > Gantt (Ectobox), id 147, `system.kanoa.config.addNavigationItem`
- Data window: Enterprise B / fillerproduction, Feb 15 to Mar 1 2026 (where block density is highest)
- Balchem original swapped: `Ecto/BalChem/kanoa/scheduler/operationsGantt` ->
  `embedded/equipmentScheduleEditor` (the `ia.display.equipmentschedule` host)
