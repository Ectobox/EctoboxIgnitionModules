# Ectobox Perspective Component Suite

All of Ectobox's Perspective controls in **one module** for Ignition 8.3: one download, one install and one gateway
restart, instead of eight.

| In the suite | What it is | Docs |
|---|---|---|
| **Datagrid** and **Pivot Grid** | A data grid for real plant data: bound or gateway-paged rows, typed editing, calculated columns, trees, live tags, totals, Excel/CSV/PDF export, and a pivot grid. | [Datagrid.md](Datagrid.md) |
| **Breadcrumb** | Pick an asset by its place in the plant: up, sideways and down in one click, from your asset table or the tag tree, with status dots. | [Breadcrumb.md](Breadcrumb.md) |
| **Process Map** | Belts, pipes, silos, tanks, pumps, valves, instruments and machines with live states, laid out by how they connect, so one map fits a wall screen and a phone. | [ProcessMap.md](ProcessMap.md) |
| **Alerts** | Toasts, message boxes and loading overlays, fired from any script. | [Alerts.md](Alerts.md) |
| **Charts** | Sixteen animated SVG charts, including an SPC control chart, a state timeline, a Sankey, a mind map and a timeline. | [Charts.md](Charts.md) |
| **Diagrams** | Flowcharts, state, sequence and Gantt diagrams written as text, live from tags. | [Diagrams.md](Diagrams.md) |
| **Drag List** | A list the user reorders by dragging. | [DragList.md](DragList.md) |
| **Dropdown Button** | One button that opens a menu of actions. | [DropdownButton.md](DropdownButton.md) |
| **Schedule** | A drag-first equipment schedule / planner. | [Schedule.md](Schedule.md) |
| **Ecto Ignition Library** | `system.ectobox.rate`: debounce and throttle for scripts. | [EctoboxIgnitionLibrary.md](EctoboxIgnitionLibrary.md) |

Every control works exactly as it did as a module of its own: the same components in the Designer palette, the same
properties and events, and the same `system.ectobox.*` script functions.

## Install

1. Download **`Ectobox-Perspective-Component-Suite-8.3.modl`** from the
   [Releases](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Ectobox-Perspective-Component-Suite-8.3.modl) list.
2. In the gateway's web interface, open the **Modules** page → **Install or Upgrade a Module** → pick the file, and
   accept the Ectobox certificate the first time.
3. Restart the gateway when it asks.

## Moving from the separate modules

If your gateway has any of the eight controls installed as separate modules, the suite replaces them. Your projects
need no changes.

1. On the gateway's **Modules** page, **Uninstall** each of these that you have:

   | Module | Module id |
   |---|---|
   | Ectobox Datagrid | `com.ectobox.datagrid` |
   | Ectobox Alerts | `com.ectobox.alerts` |
   | Ectobox Charts | `com.ectobox.charts` |
   | Ectobox Diagrams | `com.ectobox.diagrams` |
   | Drag List | `com.ectobox.draglist` |
   | Dropdown Button | `com.ectobox.dropdownbutton` |
   | Ectobox Schedule | `com.ectobox.schedule` |
   | Ecto Ignition Library | `com.ectobox.ignition.ectolib` |

2. Install the suite (above).
3. Restart the gateway **once**. The uninstalls and the install all take effect in that one restart.
4. Open the Designer: the controls are in the palette where they were.

**Things worth knowing**

- **Uninstall all eight before you restart.** A separate module left installed registers the same components as the
  suite, and whichever starts last is used, so the gateway could quietly keep running an older control. The suite
  checks at startup and logs a warning naming any module still installed, from the logger `EctoboxSuite.Gateway`.
- **Use the Modules page to uninstall, not the file system.** Deleting a `.modl` file by hand leaves the module on the
  gateway's list, and the gateway logs "The file for module ... is missing" at every start until you uninstall it on
  the Modules page.
- **Saved Datagrid layouts carry over.** Each user's column layout is kept in the gateway's internal database, and the
  suite reads it from the same place.
- **One startup line.** The suite logs `Ectobox Perspective Component Suite started: 10 controls.` instead of each
  control's own startup lines. If one control ever fails to start, the line names it and the others still run.
- **Pages reload once.** As after any module change, open Perspective sessions reconnect after the restart and pick up
  the new module.
- **Custom CSS keeps working.** Every control's CSS classes are unchanged. Only the address the browser loads each
  control's scripts from has moved, to `/res/ectobox/`; no project refers to it.

## Feedback

Found a problem with the suite, or a control that behaves differently than it did on its own? Please
[open an issue](https://github.com/Ectobox/EctoboxIgnitionModules/issues).
