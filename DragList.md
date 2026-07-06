# Drag List

**⬇ [Download the latest module (v1.0.8)](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Drag-List-8.3.modl)**

A sortable Perspective list-view component for Ignition 8.3. Like the built-in **Flex Repeater**, you point it at a view and pass an `instances` array to render one view per row — except in Drag List the user can drag rows to reorder them at runtime, and the new order is reported back to your scripts.

## Install

1. Download the latest `Drag-List.modl` from the [Releases](../../releases) page (or run `./gradlew build` and grab `build/Drag-List.modl`).
2. In the gateway, **Config → Modules → Install or Upgrade a Module** and pick the file.
3. Open a project in the Designer; **Drag List** appears in the component palette under the **Ectobox** category.

## Quick start

1. Make a small view that takes a few input parameters (e.g. `Title: string`, `Body: string`). Lay out the row contents however you want.
2. Drop a **Drag List** onto your view.
3. Set `viewPath` to the row view (use the dropdown).
4. Bind `instances` to an array of objects whose keys match your row view's params, e.g.:
   ```json
   [
     { "Title": "Apple",  "Body": "..." },
     { "Title": "Banana", "Body": "..." },
     { "Title": "Cherry", "Body": "..." }
   ]
   ```
5. Drag rows around. 

## Properties

| Property | Type | Default | Notes |
|---|---|---|---|
| `viewPath` | `string` | `""` | The view rendered for each row. Designer shows a view picker dropdown. |
| `instances` | `array` | `[]` | One object per row. Each object's keys are passed as the embedded view's params. |
| `dragHandle` | `"row" \| "rightStrip"` | `"row"` | `row` = entire row is grabbable. `rightStrip` = only a vertical strip on the right edge is grabbable (use this when your row view captures pointer events itself). |
| `handleWidth` | `integer` | `8` | Width in pixels of the right-edge handle, only used when `dragHandle = "rightStrip"`. |
| `gap` | `integer` | `4` | Pixel gap between rows. |
| `itemHeight` | `integer` | `80` | Pixel height of each row. |
| `showDropIndicator` | `boolean` | `true` | Show the highlighted drop-target border while dragging. |
| `showRemoveButton` | `boolean` | `false` | Show a per-row remove button (close icon) on the right edge. Clicking it removes the row from the local view and fires `onItemRemoved`. Honors `writeBackInstances` the same way reorder does. |
| `writeBackInstances` | `boolean` | `false` | **Off by default.** When true, the component writes the reordered+reindexed array back into the `instances` prop on every drop. **Dangerous if `instances` is bound** — can clobber the binding source. Prefer the `onItemsReordered` event for persistence. |
| `style` | `object` | `{}` | Standard Perspective style-properties object. |

### `sortOrderID`

Each row's view receives a `sortOrderID` param equal to its current 0-based display index. This is **always** injected at render time, regardless of whether the source data contains a `sortOrderID` field. Reorder a row → its `sortOrderID` updates immediately the next render.

## Events

### `onItemsReordered`

Fired when the user finishes a drag-reorder.

| Field | Type | Description |
|---|---|---|
| `oldIndex` | `integer` | The dragged row's original 0-based position. |
| `newIndex` | `integer` | The 0-based position the row was dropped into (also its new `sortOrderID`). |
| `movedItem` | `object` | The instance object that was dragged, with its new `sortOrderID` set. |
| `instances` | `array` | The full instances array after the reorder, with refreshed `sortOrderID` values. |

### `onItemRemoved`

Fired when the user clicks a row's remove button (requires `showRemoveButton = true`). The component removes the row from its local view immediately — the same optimistic, in-session behavior as reorder — and leaves persisting the change to your handler.

| Field | Type | Description |
|---|---|---|
| `index` | `integer` | The removed row's 0-based display position before removal. |
| `item` | `object` | The instance object that was removed (its `sortOrderID` is its position at removal time). |
| `instances` | `array` | The full instances array *after* the removal, with refreshed `sortOrderID` values. |

Removal follows the same persistence philosophy as reorder: don't enable `writeBackInstances` against a bound `instances`; instead write the new array back to *your* source from the event. For example, dropping the removed item from a custom prop on the parent view:

```python
# onItemRemoved script
self.getSibling("..").custom.ListItems = event.instances
```

Or delete the row from the database by its id and let the binding refresh the list:

```python
# onItemRemoved script
system.db.runPrepUpdate(
    "DELETE FROM list_items WHERE id = ?",
    [event.item.id],
    "MyDatabase",
)
```

## Persistence — recommended pattern

Don't enable `writeBackInstances` against a bound `instances`. Instead, write the new order back to *your* source from the `onItemsReordered` event. A few patterns:

### Write the new order back to a custom prop on the parent view

```python
# onItemsReordered script
self.getSibling("..").custom.ListItems = event.instances
```

(adjust the path to wherever your source array actually lives). The binding refreshes the component cleanly on the next sync. Simple and good for in-session reordering.

### Persist the full new order to a database

When each row has an `id` column and you want the database to be the source of truth for sort order. Wrapping the loop in a transaction makes the whole reorder atomic — either every row gets the new index or none of them do:

```python
# onItemsReordered script — persist sort order to SQL
db = "MyDatabase"
txId = system.db.beginTransaction(database=db, timeout=5000)
try:
    for item in event.instances:
        system.db.runPrepUpdate(
            "UPDATE list_items SET sort_order = ? WHERE id = ?",
            [item.sortOrderID, item.id],
            tx=txId,
        )
    system.db.commitTransaction(txId)
except:
    system.db.rollbackTransaction(txId)
    raise
finally:
    system.db.closeTransaction(txId)
```

For larger lists, swap the per-row `runPrepUpdate` for a single `CASE WHEN id = ? THEN ? ... END` update, or a named query that takes the `event.instances` payload directly.

### Use `movedItem` / `oldIndex` / `newIndex` when you only care about *what* moved

Sometimes the whole array isn't interesting — you just want to react to the single change. Two examples:

**Audit log of who moved what, when:**

```python
# onItemsReordered script — write a single audit row
system.db.runPrepUpdate(
    """INSERT INTO list_audit
       (item_id, item_title, old_position, new_position, moved_at, moved_by)
       VALUES (?, ?, ?, ?, NOW(), ?)""",
    [
        event.movedItem.id,
        event.movedItem.Title,
        event.oldIndex,
        event.newIndex,
        self.session.props.auth.user.userName,
    ],
    "MyDatabase",
)
```

**Notify another part of the app that one item moved** (e.g. so a Kanban-style board on another screen can animate just that card):

```python
# onItemsReordered script — broadcast just the change
system.util.sendMessage(
    project=system.util.getProjectName(),
    messageHandler="onListItemMoved",
    payload={
        "id":   event.movedItem.id,
        "from": event.oldIndex,
        "to":   event.newIndex,
    },
    scope="S",  # session-scoped
)
```

## Theming / CSS

All public class hooks are at single-class specificity (0,1,0) so a downstream rule with the same selector wins easily.

| Class | Purpose |
|---|---|
| `.ectobox-drag-list` | List container |
| `.ectobox-drag-list__row` | A row |
| `.ectobox-drag-list__row.is-hovered` | Pointer is over a row (no drag in progress) |
| `.ectobox-drag-list__row.is-dragging` | This row is being dragged |
| `.ectobox-drag-list__row.is-drop-target` | Current drop target |
| `.ectobox-drag-list__row--row-handle` | Whole-row grab is active |
| `.ectobox-drag-list__view` | Wrapper around the embedded view |
| `.ectobox-drag-list__handle` | Right-edge handle (rightStrip mode) |
| `.ectobox-drag-list__remove` | Per-row remove button (when `showRemoveButton`) |
| `.ectobox-drag-list__drop-indicator` | Drop position line |
| `.ectobox-drag-list__empty` | Empty/placeholder text |

Override example:

```css
.ectobox-drag-list__row.is-hovered {
    background: var(--my-theme-row-hover-bg);
    border-color: var(--my-theme-accent);
}
```
