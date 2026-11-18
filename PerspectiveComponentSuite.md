# Ectobox Perspective Component Suite

All of Ectobox's Perspective controls in **one module** for Ignition 8.3: one download, one install and one gateway
restart, instead of eight.

| In the suite | What it is | Docs |
|---|---|---|
| **Datagrid** and **Pivot Grid** | A data grid for real plant data: bound or gateway-paged rows, typed editing, calculated columns, trees, live tags, totals, Excel/CSV/PDF export, and a pivot grid. | [Datagrid.md](Datagrid.md) |
| **Breadcrumb** | Pick an asset by its place in the plant: up, sideways and down in one click, from your asset table or the tag tree, with status dots. | [Breadcrumb.md](Breadcrumb.md) |
| **Process Map** | Belts, pipes, silos, tanks, pumps, valves, instruments and machines with live states, laid out by how they connect, so one map fits a wall screen and a phone. | [ProcessMap.md](ProcessMap.md) |
| **Ribbon** | An Office-style command bar: tabs of grouped commands with Lucide icons that shrink to fit any screen, one event for every command, and state you can bind. | [Ribbon.md](Ribbon.md) |
| **Speed Dial** | One round button that opens into a few actions, flying out on springs in a line, an arc or a ring you pick with a press, flick and release; one event for every pick. | [SpeedDial.md](SpeedDial.md) |
| **Tabs** | A container that shows its contents a tab at a time, its tabs' contents binding to each other directly; labels never clipped, a More menu when they don't fit, counts, locks, close and drag, and a wizard with no strip. | [Tabs.md](Tabs.md) |
| **Stepper** | The steps of a task: where the user is, what is done and what has a problem; linear steps that open only when the ones before are done, a fold to "Step 2 of 5" when it doesn't fit, and with a Tabs, a wizard. | [Stepper.md](Stepper.md) |
| **Segmented Control** | One choice from a few, all in view, with a thumb that slides to the one picked; soft or solid in any tone, counts, icons only, and a touch size for panels. | [Segmented.md](Segmented.md) |
| **Chips** | Pick one, tick several, type your own list (checked, unique, up to a max, in the order you drag) or show a status; chips pop in, shrink away and close up on a spring. | [Chips.md](Chips.md) |
| **Search Select** | A search box that picks one value: every typed word must match the row (label, second line or hidden keywords), the best matches first; cards, groups and disabled rows; barcode scanners pick the row they name, even from another field; a search on the gateway for big lists. | [SearchSelect.md](SearchSelect.md) |
| **Dropdown** | Pick from a list, or type to filter: icons, groups, trailing state, disabled rows with a reason; status marks with how far each has got, a parent's rolled up from its children; a list that follows its parent; several at once; long lists stay quick; opens down or up on a spring, or as a sheet on a phone. | [Dropdown.md](Dropdown.md) |
| **Callout** | A message inside the page in a tone (info, good, warning, bad, neutral): an icon, a title and text with **bold** and `code`, actions that fire onAction, and an × that closes it (dismissed binds both ways). Soft, outline or edge, four sizes. | [Callout.md](Callout.md) |
| **Side Navigation** | An app's menu down the left that fits the screen: full, a rail of icons, or hidden behind your hamburger, by the window's width or the user's choice. Opens pages by URL and follows the URL for the current item; counts, number keys 1 to 9, sections and sub-items; a search, folding sections and a rail by section for menus with dozens of pages; built for a shell page. | [SideNavigation.md](SideNavigation.md) |
| **Stat Card** | One number that matters: a label, the number in its tone, a unit, a caption and an icon; the change against a previous value, green or red by which way is good; a sparkline; counts to a new value; clickable to filter a table. | [StatCard.md](StatCard.md) |
| **Chat** | A conversation on a screen: mine on the right, theirs with a face and a name, day lines and system lines in a tone; quick replies; photos from a phone or tablet camera, shrunk before they are sent; a New line after the last one read; a sent message waits, faded, until your script stores it (Not sent, with Retry). A touch size for gloves and tablets. | [Chat.md](Chat.md) |
| **Upload** | Files to the gateway, checked before they are sent and by content when they arrive: to a script in memory, to a folder, or a client's CSV or Excel file as rows the person checks before Import (problems by Excel row, columns matched by name). Drop, browse, paste cells from Excel, or take a photo. | [Upload.md](Upload.md) |
| **Help Requests** | An andon: a Help Call on each station (topic and detail tiles, a note, photos), a Help Queue for responders (take, on my way, I'm here, running again in, done, chat) and a Help Board for the wall, with a gateway service that routes each call to a team, escalates when nobody takes it, and keeps calls in your database and photos as files. Dormant until you give it a database; or route calls through your own system with `source` props. | [HelpRequests.md](HelpRequests.md) |
| **Badge** | A small status label or count (NEW, Paused, 12, 99+) or a status dot with words beside it that can pulse, alone, on its own icon, or on any component dropped into it; soft, solid or outline in any tone or your own colour, four sizes, clickable when asked. | [Badge.md](Badge.md) |
| **Avatar** | A person as a picture or initials in their own stable colour (never red or amber), with a status dot and a count; a machine or site as a rounded tile; the name beside it; a group that folds to +3. | [Avatar.md](Avatar.md) |
| **Alerts** | Toasts, message boxes and loading overlays, fired from any script. | [Alerts.md](Alerts.md) |
| **Charts** | Sixteen animated SVG charts, including an SPC control chart, a state timeline, a Sankey, a mind map and a timeline. | [Charts.md](Charts.md) |
| **Diagrams** | Flowcharts, state, sequence and Gantt diagrams written as text, live from tags. | [Diagrams.md](Diagrams.md) |
| **Drag List** | A list the user reorders by dragging. | [DragList.md](DragList.md) |
| **Dropdown Button** | One button that opens a menu of actions. | [DropdownButton.md](DropdownButton.md) |
| **Schedule** | A drag-first equipment schedule / planner. | [Schedule.md](Schedule.md) |
| **Ecto Ignition Library** | `system.ectobox.rate`: debounce and throttle for scripts. `system.ectobox.excel`: read Excel workbooks and CSV files into Datasets, and write them. | [EctoboxIgnitionLibrary.md](EctoboxIgnitionLibrary.md) |

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
- **One startup line.** The suite logs `Ectobox Perspective Component Suite started: 26 controls.` instead of each
  control's own startup lines. If one control ever fails to start, the line names it and the others still run.
- **Pages reload once.** As after any module change, open Perspective sessions reconnect after the restart and pick up
  the new module.
- **Custom CSS keeps working.** Every control's CSS classes are unchanged. Only the address the browser loads each
  control's scripts from has moved, to `/res/ectobox/`; no project refers to it.

## Feedback

Found a problem with the suite, or a control that behaves differently than it did on its own? Please
[open an issue](https://github.com/Ectobox/EctoboxIgnitionModules/issues).
