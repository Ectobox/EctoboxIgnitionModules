# Ectobox Ignition Modules

Free modules for [Inductive Automation's Ignition](https://inductiveautomation.com/) 8.3, built and maintained by [Ectobox](https://www.ectobox.com/).

We're a systems integration team that spends a lot of time in Ignition, and along the way we kept building small tools to smooth over the rough edges — a better script console, source control that doesn't require server access, a proper theme editor, a handful of Perspective components we wished shipped in the box, some charts, a bit of session-global UI, and a few scripting helpers. Nothing groundbreaking — just things that made our own projects a little nicer to work in. Rather than keep them to ourselves, we're releasing them here for free.

Everything in this repo is offered as-is, at no cost. If you find a module useful, great — that's the whole point.

---

## The Modules

### Designer & Gateway Tools

| Module | What it does | Docs | Download |
|---|---|---|---|
| **ScripTide** | Replaces the built-in Script Console with a full script IDE — tabs, autocomplete, a saveable script library, snippets, execution history, and a lightweight testing framework. | [ScripTide8.3.md](ScripTide8.3.md) | [v1.0.8](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Script-IDE-8.3.modl) |
| **Git for Designer** | Commit, push, and pull your Ignition project straight from the gateway — no remoting into the server or spinning up a Docker VM to get source control. | [GitForDesigner8.3.md](GitForDesigner8.3.md) | [v1.0.17](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Git-For-Designer-8.3.modl) |
| **Theme Editor** | A Perspective theme (CSS/JSON) editor that lives inside the Designer — syntax highlighting, CSS autocomplete, find/replace, and one-click refresh, with no filesystem access to the gateway required. | [ThemeEditor8.3.md](ThemeEditor8.3.md) | [v1.0.1](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Themes-Editor-8.3.modl) |
| **Designer Helpers** | A catch-all for the little things — currently a one-click full project rescan and a graceful gateway restart. | [DesignerHelpers8.3.md](DesignerHelpers8.3.md) | [v1.0.6](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Ectobox-Helpers-8.3.modl) |

### Perspective Components

| Module | What it does | Docs | Download |
|---|---|---|---|
| **Alerts** | Script-invoked, session-global Perspective UI — stacking toasts, modal message boxes that return the user's choice, and full-screen loading overlays. Fire any of them from a single line of script; all CSS ships in the module, so there's nothing to add to a project theme. | [Alerts.md](Alerts.md) | [v1.0.22](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Ectobox-Alerts-8.3.modl) |
| **Charts** | Eleven animated chart components — line, area, column, bar, scatter, bubble, pie, donut, gauge, sparkline, and heatmap — hand-drawn as crisp SVG (no charting library), with a curated palette and automatic light/dark theming. | [Charts.md](Charts.md) | [v1.0.8](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Ectobox-Charts-8.3.modl) |
| **Drag List** | A sortable list-view component. Point it at a view and pass an array of instances (like a Flex Repeater), and users can drag to reorder rows at runtime — with the new order reported back to your scripts. | [DragList.md](DragList.md) | [v1.0.8](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Drag-List-8.3.modl) |
| **Dropdown Button** | A Bootstrap-style dropdown button — a single trigger that opens a configurable popup menu. Great for a per-row "more actions" button in a table, where the menu never gets clipped by scrolling containers. | [DropdownButton.md](DropdownButton.md) | [v1.0.1](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Dropdown-Button-8.3.modl) |
| **Schedule** | A modern equipment schedule / planner — a cleaner take on the stock Equipment Schedule. Drag jobs between swim lanes (with per-lane accept rules), resize and multi-select, drop objects onto the timeline to create cards, and dress cards with badges, a progress bar, a colored icon block, and per-lane or plant-wide state bands. Curated palette, automatic light/dark, horizontal or vertical. | [Schedule.md](Schedule.md) | [v1.0.0](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Ectobox-Schedule-8.3.modl) |

### Scripting

| Module | What it does | Docs | Download |
|---|---|---|---|
| **Ecto Ignition Library** | Adds Ectobox scripting utilities to the `system.*` namespace — starting with proper rate-limiting (`debounce`, `throttle`, `cancel`, `flush`), with every function documented right in the Designer's autocomplete. | [EctoboxIgnitionLibrary.md](EctoboxIgnitionLibrary.md) | [v1.0.3](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/EctoIgnitionLib-8.3.modl) |

---

## Installation

Every module installs the same way:

1. Download the module's `.modl` file using the **Download** links above.
2. In the Ignition Gateway, go to **Config → Modules** and click **Install or Upgrade a Module**.
3. Upload the `.modl` file and restart the gateway or Designer if prompted.

See each module's documentation (linked above) for specifics.

---

## Support & Feedback

These are passion projects, and feedback genuinely shapes where they go. If you hit a bug, have an idea, or just want to tell us how you're using something, please open an issue.

For more about who we are and what we do, visit [ectobox.com](https://www.ectobox.com/).
