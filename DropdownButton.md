# Dropdown Button

**⬇ [Download the latest module (v1.0.1)](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Dropdown-Button-8.3.modl)**

A Bootstrap-style dropdown button for Ignition 8.3 Perspective. A single trigger button (a horizontal ellipsis by default) opens a popup menu of configurable items — ideal for a per-row "more actions" button in a table or grid, where Perspective has no good built-in answer.

![Sample](https://github.com/user-attachments/assets/dbacde42-5bf7-48c4-b420-f75169c1d78e)

The menu is rendered in a portal with fixed positioning, so it is **never clipped** by a scrolling table, flex container, or coordinate container, and it should flip automatically when there isn't room below the button.

## Install

1. Download the latest `Dropdown-Button.modl` from the Releases page (or run `./gradlew build` and grab `build/Dropdown-Button.modl`).
2. In the gateway, **Config → Modules → Install or Upgrade a Module** and pick the file.
3. Open a project in the Designer; **Dropdown Button** appears in the component palette under the **Ectobox** category.

## Quick start

1. Drop a **Dropdown Button** onto a view (e.g. into a table column's view, or next to your grid).
2. In the property editor, click **+** on `items` — a new item is added with defaults, just like adding columns to a table. Set its `text`, optional `icon.path` (e.g. `material/edit`), and a stable `value` (e.g. `"edit"`).

<img width="320" height="755" alt="image" src="https://github.com/user-attachments/assets/03bb7375-6674-4115-a910-452505e4e02d" />

3. Right-click the component → **Configure Events** → `onItemActionPerformed`, and branch on the payload:

```python
# onItemActionPerformed script
if event.value == "edit":
    # open your edit popup, using whichever row is selected in the grid
    ...
elif event.value == "delete":
    ...
```

<img width="1325" height="371" alt="image" src="https://github.com/user-attachments/assets/35b58c4b-bc79-4acb-be46-8976e4d178ce" />



## Properties

| Property | Type | Default | Notes |
|---|---|---|---|
| `button.text` | `string` | `""` | Trigger button text. Empty = icon-only ellipsis button. |
| `button.icon.path` | `string` | `"material/more_horiz"` | Trigger icon, `library/iconName` shorthand. `material/more_vert` for a vertical ellipsis, empty for none. |
| `button.icon.color` | `color` | `""` | Empty inherits the button text color. |
| `button.title` | `string` | `""` | Tooltip for the trigger button. |
| `button.style` | `object` | `{}` | Style for the trigger button (classes + inline styles). |
| `items[]` | `array` | `[]` | The menu. Click **+** to add an item with defaults. |
| `items[x].text` | `string` | `"Menu Item"` | Display text. |
| `items[x].icon.path` | `string` | `""` | Optional icon left of the text. |
| `items[x].icon.color` | `color` | `""` | Empty inherits the item text color. |
| `items[x].value` | `string \| number \| boolean` | `""` | Stable identifier passed in every event payload — branch on this in scripts, not on display text. |
| `items[x].enabled` | `boolean` | `true` | Disabled items are greyed out and fire no events. |
| `items[x].visible` | `boolean` | `true` | Hidden items are not rendered. |
| `items[x].style` | `object` | `{}` | Per-item style (classes + inline styles). |
| `menuPlacement` | `enum` | `"bottom-start"` | `bottom-start`, `bottom-end`, `top-start`, `top-end`. Flips automatically when there's no room. |
| `menuMinWidth` | `integer` | `160` | Menu minimum width in px; grows to fit content. |
| `closeOnItemClick` | `boolean` | `true` | Close the menu after an item is clicked. |
| `enabled` | `boolean` | `true` | Disables the trigger button entirely. |
| `style` | `object` | `{}` | Standard Perspective style prop for the component root. |

## Events

Perspective's event system attaches scripts at the component level, so per-item events carry the item's identity in the payload — every `onItem*` event below has the same payload: `index` (0-based position in `items`), `text`, `value`, and `item` (the full item object). Branch on `event.value`.

| Event | Fires when |
|---|---|
| `onItemActionPerformed` | A menu item is activated — clicked, or Enter/Space while keyboard-focused. The per-item equivalent of a Button's `onActionPerformed`. |
| `onItemDoubleClicked` | A menu item is double-clicked. Only reachable when `closeOnItemClick` is `false` (otherwise the first click closes the menu). |
| `onItemMouseEnter` | The pointer enters an enabled menu item. |
| `onItemMouseLeave` | The pointer leaves an enabled menu item. |
| `onMenuToggled` | The menu opens or closes. Payload: `open` (boolean). |

> **Note on hover events:** each `onItemMouseEnter`/`onItemMouseLeave` is a round-trip to the gateway. Leave them unconfigured unless you need them.

## Behavior details

- **Outside click / Escape** closes the menu.
- **Scrolling or resizing** while open repositions the menu so it stays glued to the trigger.
- Keyboard: the trigger and the items are real `<button>` elements, so Tab/Enter/Space work natively; Enter/Space on an item fires `onItemActionPerformed`.

## Theming / CSS

All hooks are low-specificity classes; menu colors follow Perspective theme variables (`--container`, `--neutral-*`, `--callToAction`) with sane fallbacks.

| Class | Purpose |
|---|---|
| `.ectobox-dropdown-button` | Component root |
| `.ectobox-dropdown-button__trigger` | Trigger button (`.is-open` while menu shown, `--icon-only` when no text) |
| `.ectobox-dropdown-button__menu` | The popup menu (portaled to `<body>`) |
| `.ectobox-dropdown-button__item` | A menu item (`.is-disabled` when disabled) |
| `.ectobox-dropdown-button__item-icon` / `__item-text` | Parts of an item |
| `.ectobox-dropdown-button__empty` | Placeholder when no items are configured |

Override example:

```css
.ectobox-dropdown-button__item:hover:not(:disabled) {
    background: var(--my-theme-hover-bg);
}
```
