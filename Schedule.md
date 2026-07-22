# Ectobox Schedule

A modern, Apple-clean **equipment schedule / planner for Ignition 8.3 Perspective** — a ground-up,
higher-standards take on the stock Equipment Schedule. Drag jobs between swim lanes, drop objects onto
the timeline to create cards, and dress each card with badges, a progress bar, a colored left block
with an icon, and per-lane or plant-wide state bands. Hand-built (no third-party scheduling library),
with a curated palette and automatic light/dark theming.

## What's included

| Component | Component id | What it does |
|---|---|---|
| **Schedule Timeline** | `ectobox.schedule.timeline` | The scheduler: swim lanes of event cards on a time axis, with drag-between-lanes, resize, drop-to-create, badges, progress bars, and state bands. |
| **Schedule Drag Source** | `ectobox.schedule.drag-source` | A palette of draggable chips; drag one onto a Schedule Timeline to create a card — no scripting needed. |

Both appear in the Designer palette under **Ectobox Schedule**.

## Why it's different from the stock Equipment Schedule
- **Move jobs between lanes** — drag a card from one piece of equipment to another (e.g. machine 4 is
  busy, so move the batch to machine 3), with **declarative rules** for what each lane will accept.
- **Drop to create** — drag any object with at least an `ID` and `Name` onto the timeline and it
  becomes a card; optional `StartDate`/`EndDate`/`Tags`/`Color`/`Type`/`Progress`/`Icon` are honored.
- **Cards that communicate** — optional **badges** (type, owner, priority…), a **progress bar**, and a
  colored **left block with an icon** (Material, [Lucide](https://lucide.dev), or an image).
- **State bands** — per-lane spans (running / idle / down / setup) painted behind the cards, plus
  plant-wide bands for shifts and breaks; either can optionally block drops.
- **Apple-style palette, automatic light/dark** — a curated 12-color set; cards are colored by *what
  they are* (their type), so a job keeps its color when moved between lanes.
- **Horizontal or vertical** orientation, multi-select **bulk move**, and edge **resize**.

<!-- screenshots to be added -->

## Install

1. Download **`Ectobox-Schedule-8.3.modl`** from the [Releases](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Ectobox-Schedule-8.3.modl) list.
2. Gateway → **Config → Modules → Install or Upgrade a Module** → pick the file (accept the Ectobox certificate on first install).
3. In the Designer, the components appear in the palette under **Ectobox Schedule**.

## Quick start

1. Drop a **Schedule Timeline** onto a view.
2. Bind **`items`** (the swim lanes) and **`events`** (the cards) to named queries, tags, or scripted
   arrays — or start from the built-in sample data.
3. To let users create cards by dragging, drop a **Schedule Drag Source** nearby and set its `items`.

```python
# items — the swim lanes:
[ { "id": "m3", "label": "Machine 3", "type": "cnc", "accept": { "types": ["cnc"] } },
  { "id": "m4", "label": "Machine 4", "type": "cnc" } ]

# events — the cards:
[ { "id": "e1", "itemId": "m3", "name": "Batch #4471",
    "start": "2026-07-21T08:00:00", "end": "2026-07-21T11:30:00",
    "type": "cnc", "badges": ["Rush", {"name": "Owner: Sam", "color": "orange"}],
    "progress": { "enabled": true, "value": 62 },
    "leftBlock": { "enabled": true, "icon": { "path": "lucide/factory" } } } ]
```

## Key properties (Schedule Timeline)

| Prop | Notes |
|---|---|
| `items` | Swim lanes. Each: `id`, `label`, `type`, `color`, `icon`, and optional `accept` rules (`types`, `eventIds`, `denyTypes`). |
| `events` | Cards. Each: `id`, `itemId`, `name`, `start`, `end`, plus optional `color`, `type`, `badges`, `leftBlock`, `progress`, `movable`, `resizable`, `lockedToItem`, `badgePlacement`. |
| `laneStates` | Per-lane state spans behind the cards: `itemId`, `start`, `end`, `state`, `color`, `label`, `blocksDrop`. |
| `globalBands` | Bands spanning all lanes (shifts/breaks): `start`, `end`, `label`, `color`, `opacity`, `blocksDrop`. |
| `timeline` | `start`, `end`, `zoom` (month/day/12-hr/8-hr/6-hr/3-hr/hours/15-min/minutes), `snapMinutes`, `showCurrentTime`, `currentTime`. |
| `navigation` | Built-in toolbar: `enabled` (prev / today / next + range label) and `showZoom`. Panning/zooming writes `timeline.start`/`end` back and fires `onRangeChanged`. |
| `orientation` | `horizontal` (lanes are rows) or `vertical` (lanes are columns). |
| `overlap` | `stack` (pack overlapping cards into sub-rows) or `reject` (refuse an overlapping move/drop). |
| `addEnabled` / `moveEnabled` / `resizeEnabled` / `deleteEnabled` / `dropEnabled` | Toggle each interaction. |
| `selectedEvent` / `selectedEvents` | The current selection, written back for your bindings. |
| `badgePlacement`, `cornerRadius`, `density`, `rowHeight`, `laneHeaderWidth`, `palette`, `title` | Appearance. |

## Events

The component **optimistically updates its own `events` prop** (so bindings see changes immediately)
**and** fires a matching event so you can persist or audit:

| Event | Payload |
|---|---|
| `onEventMoved` | `eventId, fromItemId, toItemId, oldStart, oldEnd, newStart, newEnd, movedCount` |
| `onEventResized` | `eventId, itemId, oldStart, oldEnd, newStart, newEnd` |
| `onEventAdded` | `eventId, itemId, start, end` |
| `onEventDropped` | `eventId, itemId, start, end, source` (the raw dropped object) |
| `onEventDeleted` | `eventId, itemId` |
| `onEventClicked` / `onSelectionChanged` | `eventId, itemId` (+ `selected[]` for selection changes) |
| `onEventDoubleClicked` | `eventId, itemId, event` — open your own editor here (see below) |
| `onRangeChanged` | `start, end` — the visible window changed via the navigation toolbar |
| `onMoveRejected` | `eventId, fromItemId, attemptedItemId, reason` (`laneAccept` / `denyType` / `stateBandBlocked` / `lockedToItem` / `notMovable` / `overlap`) |

## Editing cards

The component is intentionally **unopinionated about editing** — rather than a fixed built-in form, it
fires **`onEventDoubleClicked`** so you can open your own editor (a popup, a docked panel, whatever
fits). Bind the schedule's `events` to something writable (a session/view custom property, a tag, a
named-query-backed dataset), open your editor on double-click, and on save update that backing store —
the schedule re-renders from the bound value. `selectedEvent` tells your editor which card is active.

**Worked example — a double-click popup editor.** Bind `events` to `view.custom.events`
(bidirectional, so drags/resizes persist too). On `onEventDoubleClicked`, open a small popup editor,
passing each field as its own **top-level scalar param** (not one object param — Perspective popups
populate scalar params only):

```python
# onEventDoubleClicked  (event has eventId / itemId / event)
eid = event['eventId']
evt = next((e for e in self.view.custom.events if e['id'] == eid), None)
if evt:
    system.perspective.openPopup('evtEditor', 'Schedule/EventEditor',
        params={'id': evt['id'], 'name': evt['name'], 'type': evt.get('type',''),
                'start': evt['start'], 'end': evt['end']},
        title='Edit Event', modal=True, showCloseIcon=True)
```

In the editor view, declare each param with `propConfig["params.name"] = {"paramDirection": "input"}`
and bind each field to `view.params.<field>` to **populate** it (openPopup only fills top-level scalar
params — a nested `view.params.event.name` path won't populate). On **Save**, read the field
components' **live values** (an openPopup-injected param does not accept a bidirectional write-back, so
read the inputs directly), send them, and let the schedule's view update its store:

```python
# Save button — read the input components, not the params
root = self.parent.parent            # buttons flex -> root
def fld(group, field):
    return root.getChild(group).getChild(field)
def toIso(ms):
    return None if not ms else system.date.format(system.date.fromMillis(long(ms)), "yyyy-MM-dd'T'HH:mm:ss")
payload = {
    'id':    self.view.params.id,
    'name':  fld('nameGroup', 'nameField').props.text,
    'type':  fld('typeGroup', 'typeField').props.value,      # dropdown
    'start': toIso(fld('startGroup', 'startField').props.date),  # date-time-picker (ms)
    'end':   toIso(fld('endGroup', 'endField').props.date),
    'iconPath': fld('iconGroup', 'iconField').props.value,
}
system.perspective.sendMessage('ecto.sched.saveEvent', payload=payload, scope='page')
system.perspective.closePopup('evtEditor')

# 'ecto.sched.saveEvent' handler on the schedule's view — merge edited fields by id:
edited = system.util.jsonDecode(system.util.jsonEncode(payload))
events = system.util.jsonDecode(system.util.jsonEncode(self.view.custom.events))
for e in events:
    if e['id'] == edited['id']:
        for k in ('name', 'type', 'start', 'end'):
            if edited.get(k) is not None:
                e[k] = edited[k]
        e['leftBlock'] = {'enabled': True, 'icon': {'path': edited['iconPath']}} if edited.get('iconPath') else {'enabled': False}
self.view.custom.events = events
```

## Colors, badges & icons

- **Card color** comes from the event's `type` (a curated palette color), so all "maintenance" cards
  share a color and a job keeps its color across lane moves. Set an explicit `color` to override.
- **Badges** are an array of strings (auto-colored, stable per label) or `{ name, color }` where color
  is a hex or a palette name (`blue`, `green`, `orange`, …).
- **Icons** (left block + lane headers) use a `path`: `material/<name>`, `lucide/<name>`
  ([Lucide](https://lucide.dev), rendered stroked), or an image URL / data URI.

## Theming & CSS

Surfaces, text, and gridlines follow Perspective's theme variables (`--neutral-*`, `--container`), so
**light/dark is automatic**. The palette is exposed as CSS custom properties you can override globally:

```css
:root {
  --ecto-sched-1: #0a84ff;   /* type/series color 1 */
  --ecto-sched-2: #ff9f0a;   /* ... up to --ecto-sched-12 ... */
}
```

Everything drawn is a prefixed class you can restyle: `.ecto-sched__card`, `.ecto-sched__badge`,
`.ecto-sched__progress`, `.ecto-sched__state`, `.ecto-sched__band`, `.ecto-sched__lane`, and more.
