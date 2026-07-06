# Ectobox Ignition Modules

Free modules for [Inductive Automation's Ignition](https://inductiveautomation.com/) 8.3, built and maintained by [Ectobox](https://www.ectobox.com/).

We're a systems integration team that spends a lot of time in Ignition, and along the way we kept building small tools to smooth over the rough edges — a better script console, source control that doesn't require server access, a proper theme editor, a few Perspective components we wished shipped in the box. Rather than keep them to ourselves, we're releasing them here for free.

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
| **Drag List** | A sortable list-view component. Point it at a view and pass an array of instances (like a Flex Repeater), and users can drag to reorder rows at runtime — with the new order reported back to your scripts. | [DragList.md](DragList.md) | [v1.0.8](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Drag-List-8.3.modl) |
| **Dropdown Button** | A Bootstrap-style dropdown button — a single trigger that opens a configurable popup menu. Great for a per-row "more actions" button in a table, where the menu never gets clipped by scrolling containers. | [DropdownButton.md](DropdownButton.md) | [v1.0.1](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Dropdown-Button-8.3.modl) |

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
