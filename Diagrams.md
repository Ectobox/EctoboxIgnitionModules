# Ectobox Diagrams

**Mermaid-style diagrams for Ignition 8.3 Perspective** — flowcharts and state diagrams written as
plain text, laid out automatically, and rendered as crisp SVG. No charting library, no graph-layout
library: the parser, the layout engine and the renderer are all ours.

The part that makes it an Ignition component rather than a Mermaid port: **the diagram is live.** The
spec text is a bindable prop, so it can be *generated* from a dataset or from your tag tree; and
node/edge state is a second binding, so the same diagram shows what the plant is doing right now.

```
flowchart TD
    Start([Shift start]) --> Check{Line clear?}
    Check -->|No| Clear[Clear jam] --> Check
    Check -->|Yes| Fill[Filler] --> Cap[Capper] --> Ship([Dispatch])
```

## What's included

| Component | Component id | Good for |
|---|---|---|
| **Flow Diagram** | `ectobox.diagram.flow` | Process flow, logic, routing, decisions, subgraphs. |
| **State Diagram** | `ectobox.diagram.state` | Machine modes, batch steps, CIP sequences, permit logic. |

Both appear in the Designer palette under the **Ectobox Diagrams** category.

Plus a scripting namespace for generating specs from data:

| Function | What it does |
|---|---|
| `system.ectobox.diagrams.FlowFromDataset(edges, nodes, direction, defaultShape, connector)` | Builds a flowchart from an edge list (+ optional node list). Takes a dataset, a list of dicts, or a list of lists. |
| `system.ectobox.diagrams.StateFromTransitions(transitions, direction, initial, terminal)` | Builds a state diagram from a from/to/event transition table. |
| `system.ectobox.diagrams.FromUdtStructure(tagPath, direction, style, maxDepth, includeTags, showTypes, showValues)` | Walks the live tag tree and renders the equipment hierarchy. |
| `system.ectobox.diagrams.Escape(label)` / `SafeId(text)` | Make arbitrary text safe to drop into a spec. |

## Why it's different
- **The diagram is live.** `nodeStates` / `edgeStates` recolor, relabel, badge, pulse and animate the
  diagram from tags without the spec text changing. A State Diagram with `activeState` bound to one tag
  shows the mode the machine is actually in.
- **Generated, not drawn.** Point `FromUdtStructure` at a tag folder and the equipment hierarchy draws
  itself. Feed `FlowFromDataset` a named query and the routing diagram comes from your data.
- **Nothing to lay out.** A layered (Sugiyama-style) engine handles ranking, crossing reduction, chain
  straightening and edge routing. You write what connects to what; it decides where things go.
- **No third-party bundle.** No `mermaid`, no `dagre`, no charting library — same as the Charts module.
  Small, fast, and fully ours to shape.
- **Automatic light/dark.** Nodes, edges, labels and group boxes follow the gateway theme with no
  per-theme setup, and every color is a CSS variable you can override.
- **Errors you can act on.** A spec that doesn't parse shows the offending line numbers and messages in
  the component — never a blank box that looks like a broken binding.

## Install

1. Download **`Ectobox-Diagrams-8.3.modl`** from the [Releases](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Ectobox-Diagrams-8.3.modl) list.
2. Gateway → **Config → Modules → Install or Upgrade a Module** → pick the file (accept the Ectobox certificate on first install).
3. In the Designer, the diagram components appear in the palette under **Ectobox Diagrams**.

## Quick start

1. Drop a **Flow Diagram** onto a view. It ships with a working example in its `text` prop.
2. Edit `text` — that's the whole diagram.
3. To make it live, bind `nodeStates` to a script transform returning a list of
   `{"id": ..., "state": ...}` objects.

```python
# nodeStates: one entry per node you want to say something about.
[
  {"id": "Fill",   "state": "running", "value": "180 bpm"},
  {"id": "Cap",    "state": "bad", "pulse": True, "value": "FAULT 3121"},
  {"id": "Reject", "state": "warn", "badge": "17"}
]
```

Semantic states — `active`, `good`, `warn`, `bad`, `muted`, `running`, `done` — pick themed colors for
you, so a live diagram looks right without choosing colors per node. `color` overrides them when you
need something specific.

## Supported syntax

A deliberate **subset** of Mermaid, chosen around what industrial users actually reach for. Specs paste
in from Mermaid docs and work; unsupported constructs are skipped with a warning rather than failing the
whole diagram.

**`flowchart TD | BT | LR | RL`**

- Node shapes: `[rect]`, `(round)`, `([stadium])`, `[[subroutine]]`, `[(cylinder)]`, `((circle))`,
  `{rhombus}`, `{{hexagon}}`, `[/parallelogram/]`, `[/trapezoid\]`, `>asymmetric]`
- Edges: `-->` arrow, `---` line, `-.->` dotted, `==>` thick, `--x` cross, `--o` circle, `<-->`
  bidirectional, `~~~` invisible. Extra dashes (`--->`) push the nodes further apart.
- Edge labels: `A -->|yes| B` or `A -- yes --> B`
- `subgraph Name [Title]` … `end`, including nesting
- `classDef` / `class` / `:::class` / `style`
- `A & B --> C & D` (cross product)

**`stateDiagram-v2`**

- Transitions with `A --> B : trigger`
- `[*]` start and end markers (per scope)
- `state Name { … }` composite states, `state "Long name" as X` aliases
- `X : description` second lines, `note right of X : text`
- `direction LR`, `<<choice>>`

Not supported — reported in `parse.warnings`, not silently mangled: `click`, `linkStyle`, multi-line
notes, concurrency regions, fork/join bars, and diagram kinds other than the two above
(`sequenceDiagram`, `gantt`, class and ER diagrams).

## Data & bindings

| Want | Do |
|---|---|
| A diagram from a query | Bind `text` to a script transform calling `FlowFromDataset(...)`. |
| A diagram of your plant | Bind `text` to `FromUdtStructure('[default]Packaging/Line1', ...)`. |
| A state model from a table | Bind `text` to `StateFromTransitions(...)`. |
| Live equipment status | Bind `nodeStates` to a script transform over your tags. |
| Show the current mode | Bind the State Diagram's `activeState` to one string tag. |
| Drill into a unit | Use the `onNodeClick` event — `event.id` is the node id from the spec. |

Column names are matched loosely, so a query returning `source` / `target` / `label` needs no
reshaping: `from`/`source`/`src`/`parent` and `to`/`target`/`dest`/`child` are all recognized.

`FromUdtStructure` reads the tag tree, so it runs in gateway/Perspective scope (a binding, a gateway
event, a project script called from either) — not in the Designer's script console. Every other function
works in any scope.

## Common properties

Both components share these (the State Diagram adds its own block below):

| Prop | Notes |
|---|---|
| `text` | The diagram spec. Bind it to generate the diagram from data. |
| `direction` | `TD`/`TB`/`BT`/`LR`/`RL` override for the direction in the spec. Blank = use the spec (or `TD`). |
| `nodeStates[]` | Live per-node state: `id`, `state`, `color`, `textColor`, `label`, `value` (second line), `tooltip`, `pulse`, `badge`. |
| `edgeStates[]` | Live per-edge state, matched by `id` or by `from`/`to`: `state`, `color`, `label`, `width` (0 = default), `animated`. |
| `layout` | `nodeSpacing`, `rankSpacing`, `edgeStyle` (`orthogonal`/`curved`/`straight`), `cornerRadius`, `crossingPasses`, `straightenChains`. |
| `nodeStyle` | `minWidth`, `paddingX`, `paddingY`, `cornerRadius`, `fontSize`, `maxLabelWidth`, `shadow`. |
| `edgeStyle` | `width`, `arrowSize`, `fontSize`, `labelBackground`. |
| `interaction` | `panZoom`, `fitToView`, `maxAutoZoom`, `minAutoZoom`, `showControls`, `clickableNodes`. |
| `animation` | `enabled`, `durationMs`. |
| `palette` | Optional list of colors for classed nodes (`classDef`), overriding the built-in palette. |
| `maxNodes` | Safety cap, default **400**. |
| `title` | Optional heading above the diagram. |

The two components start from different layout defaults: the Flow Diagram routes edges `orthogonal`
with 6 px node corners, while the State Diagram routes transitions `curved` with 10 px corners and a
little more spacing, which is how state models conventionally read.

The Designer property editor shows every prop with inline descriptions, and defaults are chosen so a
freshly-dropped diagram looks right immediately.

### State Diagram extras

| Prop | Notes |
|---|---|
| `activeState` | The state the machine is in *right now*. Bind it to one tag and that state highlights and pulses. |
| `visitedStates` | State ids already passed through, drawn as completed — a breadcrumb trail through a batch or CIP sequence. |
| `stateOptions.highlightActivePath` | Also light up the transitions into and out of `activeState` (default on). |
| `stateOptions.showStartEnd` | Draw the `[*]` start (filled dot) and end (ringed dot) markers. |
| `stateOptions.compositePadding` | Padding in px inside a composite state's box. |
| `stateOptions.dimInactive` | Fade every state except the active one — reads well on a large model on a big screen. |

`activeState` covers the simple "where am I now" case. Use `nodeStates` when several states need
coloring at once — one active plus two alarmed, say.

## Events

| Event | Payload |
|---|---|
| `onNodeClick` (Flow Diagram) | `id`, `label`, `shape`, `classes`, `group`. |
| `onNodeClick` (State Diagram) | `id`, `label`, `classes`, `group`, `active` (true when it's the `activeState`). |
| `onEdgeClick` (both) | `id`, `from`, `to`, `label`. |

`event.id` is the node id from the spec, so it matches the ids you use in `nodeStates`:

```python
# onNodeClick on a packaging line Flow Diagram
system.perspective.openPopup(
    'machineDetail',
    'Popups/MachineDetail',
    params={'machinePath': '[default]Packaging/Line1/{0}'.format(event.id)},
    title=event.label
)
```

## Output props

Read-only, written back by the component — check these when a diagram renders unexpectedly:

| Property | Contents |
|---|---|
| `parse` | `ok`, `errors[]` (each with a line number and message), `warnings[]` (unsupported constructs skipped, node cap hit). |
| `stats` | `nodes`, `edges`, `ranks`, `width`, `height` — the laid-out size, handy for sizing a container. |

Writes only happen when the result actually changes, so they can't loop.

## Scripting: `system.ectobox.diagrams.*`

Every function returns spec **text** — the string the component's `text` prop takes — so they drop
straight into a binding's script transform. Registered in gateway *and* Designer scope, so you get
autocomplete while you write. Arguments may be given positionally or by keyword.

### A routing diagram from the database

`FlowFromDataset` takes an edge list and, optionally, a node list that sets each node's label, shape,
class and group (a group wraps its nodes in a subgraph box). Nodes that appear only in the edge list
are created automatically, so the edge list on its own is enough.

```python
# Project library script: Diagrams
def GetLineRouting(lineId):
    edges = system.db.runPrepQuery("""
        SELECT src.Name AS source, dst.Name AS target, r.Product AS label
        FROM EquipmentRoute r
        JOIN Equipment src ON src.ID = r.FromEquipmentID
        JOIN Equipment dst ON dst.ID = r.ToEquipmentID
        WHERE r.LineID = ?
        ORDER BY r.ID
    """, [lineId])

    nodes = system.db.runPrepQuery("""
        SELECT e.Name AS id,
               e.Description AS label,
               CASE WHEN e.EquipmentType = 'Tank' THEN 'cylinder' ELSE 'rect' END AS shape,
               e.Area AS area
        FROM Equipment e
        WHERE e.LineID = ?
        ORDER BY e.ID
    """, [lineId])

    return system.ectobox.diagrams.FlowFromDataset(edges, nodes, direction='LR')
```

Bind the Flow Diagram's `text` to a transform that calls `Diagrams.GetLineRouting(value)` on the view's
line ID, and the diagram follows the routing table.

| Argument | Notes |
|---|---|
| `edges` | Required. Columns (loosely matched): `from`/`source`/`src`/`parent`/`start`, `to`/`target`/`dest`/`destination`/`child`/`end`, optional `label`/`text`/`title`/`name`/`description`, optional `connector`/`edgeStyle`/`lineStyle`/`arrow`. |
| `nodes` | Optional. `id`/`node`/`key`/`name`, `label`/`text`/`title`/`description`, `shape`/`kind`/`type`, `class`/`cssClass`/`style`/`category`, `group`/`subgraph`/`area`/`section`. |
| `direction` | `"TD"` (default), `"LR"`, `"BT"`, `"RL"`. |
| `defaultShape` | For nodes that don't set one — `rect` (default), `round`, `stadium`, `subroutine`, `cylinder`, `circle`, `rhombus`, `hexagon`, `parallelogram`, `trapezoid`. Friendly aliases work too: `box`, `decision`, `terminal`, `database`, `io`. |
| `connector` | For edges that don't set one — `"-->"` (default), `"---"`, `"-.->"`, `"==>"`. |

### Read this bit: generated ids go through `SafeId`

Names from your data become node ids via `SafeId` — letters, digits and underscores only, never starting
with a digit — so `"Filler #2"` becomes `Filler_2`. The original name stays as the label. When you build
`nodeStates` for a generated diagram, run the same names through `SafeId` so the ids line up:

```python
# Project library script: Diagrams
STATE_BY_CODE = {0: 'muted', 1: 'running', 2: 'warn', 3: 'bad'}

def GetLineStates(linePath, machineNames):
    paths = []
    for machineName in machineNames:
        paths.append('{0}/{1}/StateCode'.format(linePath, machineName))
        paths.append('{0}/{1}/Rate'.format(linePath, machineName))
    values = system.tag.readBlocking(paths)

    nodeStates = []
    for i, machineName in enumerate(machineNames):
        stateCode = values[i * 2].value
        rate = values[i * 2 + 1].value
        nodeStates.append({
            'id':    system.ectobox.diagrams.SafeId(machineName),
            'state': STATE_BY_CODE.get(stateCode, 'default'),
            'value': '{0:.0f} bpm'.format(rate or 0),
            'pulse': stateCode == 3,
        })
    return nodeStates
```

`Escape(label)` is the companion for hand-assembled specs: it quotes text containing characters the
spec syntax would otherwise eat (brackets, braces, pipes, quotes) and converts newlines to line breaks.

### A state model from a transition table

`StateFromTransitions` takes the shape a state model usually already has: from state, to state, and the
event that causes it. `initial` adds the `[*]` start marker; `terminal` (a list of state names, or rows
with a `state` column) gives each of those states an arrow out to the end marker.

```python
# Project library script: Diagrams
def GetMachineStateModel(machineTypeId):
    transitions = system.db.runPrepQuery("""
        SELECT t.FromState AS source, t.ToState AS target, t.TriggerEvent AS event
        FROM StateTransition t
        WHERE t.MachineTypeID = ?
        ORDER BY t.ID
    """, [machineTypeId])

    return system.ectobox.diagrams.StateFromTransitions(
        transitions,
        direction='LR',
        initial='Idle',
        terminal=['Aborted']
    )
```

Then bind the State Diagram's `activeState` to the capper's mode tag (for example
`[default]Packaging/Line1/Capper/Mode`) and the model shows where the machine actually is. As with flow
diagrams, state names that aren't already valid ids go through `SafeId` — `"Clearing Jam"` becomes
`Clearing_Jam` with a `state "Clearing Jam" as Clearing_Jam` alias — so bind `activeState` to that id.

| Argument | Notes |
|---|---|
| `transitions` | Required. `from`/`source`/`src`/`parent`, `to`/`target`/`dest`/`child`, optional `label`/`text`/`name`/`event`/`trigger`. |
| `direction` | `"TD"`, `"LR"`, `"BT"`, `"RL"`. Blank (default) lets the component decide. |
| `initial` | The starting state. Blank (default) = no start marker. |
| `terminal` | States that end the sequence. `None` (default) = no end markers. |

### A diagram of your tag tree

`FromUdtStructure` walks the live tag tree under a path — UDT instances labelled with their type,
folders as containers, optionally the atomic tags and their current values. Point it at an area folder
and the equipment hierarchy draws itself.

```python
# Binding on a Flow Diagram's text prop: expression binding on the view's area path, then this transform
def transform(self, value, quality, timestamp):
    return system.ectobox.diagrams.FromUdtStructure(
        tagPath=value,          # e.g. '[default]Packaging/Line1'
        direction='LR',
        style='nested',
        maxDepth=2
    )
```

| Argument | Notes |
|---|---|
| `tagPath` | The path to browse, e.g. `"[default]Packaging/Line1"`. Blank browses the provider roots. |
| `direction` | `"LR"` (default — best for hierarchies), `"TD"`, `"BT"`, `"RL"`. |
| `style` | `"tree"` (default): a box per node with parent-to-child edges, reads like an org chart. `"nested"`: folders and UDT instances become subgraph boxes around their children, reads like a plant layout — better when the tree is shallow and wide. |
| `maxDepth` | Levels below the path to descend, default `3`. Keep it small on a big provider. |
| `includeTags` | Include atomic tags, not just folders and UDT instances. Default `False`. |
| `showTypes` | Show a UDT instance's type name on a second line. Default `True`. |
| `showValues` | Read and show each tag's current value. Implies `includeTags`. Default `False`. |

## Theming & CSS

All styling ships in the module bundle, and the neutral colors follow Perspective's theme variables
(`--container`, `--neutral-*`) so **light/dark is automatic**. The diagram's own colors are CSS custom
properties you can override from a project stylesheet:

```css
:root {
  --ecto-diagram-node-fill:   #f5f7fa;
  --ecto-diagram-node-stroke: #8e8e93;
  --ecto-diagram-edge-stroke: #8e8e93;
}
```

The full set: `--ecto-diagram-node-fill`, `-node-stroke`, `-text`, `-sub-text`, `-edge-stroke`,
`-edge-muted`, `-group-fill`, `-group-stroke`, `-group-label`, `-plate` (the backing behind edge
labels), and the `-control-bg` / `-control-border` of the zoom buttons. The semantic state colors
(`active`, `good`, `warn`, `bad`, …) are fixed; to use a different color on a particular node or edge,
set `color` in its `nodeStates` / `edgeStates` entry.

Everything drawn is a prefixed class you can restyle: `.ecto-diagram__node`, `.ecto-diagram__node-shape`,
`.ecto-diagram__node-label`, `.ecto-diagram__node-sub`, `.ecto-diagram__node-badge`,
`.ecto-diagram__edge`, `.ecto-diagram__edge-line`, `.ecto-diagram__edge-arrow`,
`.ecto-diagram__edge-label`, `.ecto-diagram__group-box`, `.ecto-diagram__group-label`,
`.ecto-diagram__title`, and `.ecto-diagram__controls`.

## Performance

`maxNodes` (default **400**) caps how much is drawn; going over it reports the truncation in
`parse.warnings` rather than locking up the browser. `layout.crossingPasses` (default 8) trades tidiness
for speed on large graphs. `interaction.minAutoZoom` (default 0.55) stops a large diagram shrinking
below readability — past that point it stays legible and you pan instead of squinting. Set it to `0.05`
to always fit the whole thing in view.
