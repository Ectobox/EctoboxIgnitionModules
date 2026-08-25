# Ectobox Charts

Beautiful, animated **chart components for Ignition 8.3 Perspective** — Twelve chart types, hand-drawn as crisp SVG (no third-party charting library), with
a curated Apple-style palette, automatic light/dark theming, buttery entrance animations, and
properties that are actually pleasant to bind in the Designer.

<img width="1016" height="913" alt="msedge_FZ2PJUVpHo" src="assets/Charts-1.gif" />

```python
# Every chart takes a dataset (from a named query, historian, etc.) or a plain array of objects.
# Drop one on a view, point its `data` prop at your query, and you're done.
```

## What's included

| Chart | Component id | Good for |
|---|---|---|
| **Line** | `ectobox.chart.line` | Multi-series trends (smooth / straight / step). |
| **Area** | `ectobox.chart.area` | Trends with gradient fill; volumes. |
| **Column** | `ectobox.chart.column` | Vertical grouped bars. |
| **Bar** | `ectobox.chart.bar` | Horizontal ranked bars. |
| **Scatter** | `ectobox.chart.scatter` | Numeric X/Y correlations. |
| **Bubble** | `ectobox.chart.bubble` | Three dimensions (X, Y, size). |
| **Pie** | `ectobox.chart.pie` | Proportions. |
| **Donut** | `ectobox.chart.donut` | Proportions with a count-up total in the hole. |
| **Gauge** | `ectobox.chart.gauge` | A single value with red/amber/green thresholds. |
| **Sparkline** | `ectobox.chart.sparkline` | Compact, axis-free trend lines for tiles/tables. |
| **Heatmap** | `ectobox.chart.heatmap` | Value-colored grids (schedules, density, correlation). |
| **Control Chart** | `ectobox.chart.control` | SPC: Individuals & Moving Range with zones and run rules. |

All twelve appear in the Designer palette under the **Ectobox Charts** category.

## Why it's different
- **Hand-built SVG** — no ApexCharts/Chart.js/vendor bundle. Small, fast, and fully ours to shape.
- **Apple-style palette, always looks right** — a curated 12-color set applied automatically, so a
  multi-series chart looks good with zero configuration. Every color is a CSS variable you can override.
- **Automatic light/dark** — axes, gridlines, labels, and surfaces follow the gateway theme with no
  per-theme setup.
- **Animations that impress** — lines draw themselves in, columns/bars grow with a staggered cascade,
  pie/donut slices sweep in and explode on hover, gauges sweep + count up, scatter/bubble points pop in,
  heatmap cells wave in on a diagonal.
- **Overridable everywhere** — all styling is prefixed `.ecto-chart__*` CSS you can target from a
  project stylesheet.

<img width="503" height="449" alt="image" src="assets/Charts-2.png" />
<img width="493" height="447" alt="image" src="assets/Charts-3.png" />

## Install

1. Download **`Ectobox-Charts-8.3.modl`** from the [Releases](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Ectobox-Charts-8.3.modl) list.
2. Gateway → **Config → Modules → Install or Upgrade a Module** → pick the file (accept the Ectobox certificate on first install).
3. In the Designer, the chart components appear in the palette under **Ectobox Charts**.

## Quick start

1. Drop a chart (e.g. **Line Chart**) onto a view.
2. Bind its **`data`** prop to a dataset (named query, tag history, etc.) or set an array of objects.
3. In **`mapping`**, pick which column is the category/X axis and which columns are series — or leave it
   blank to auto-detect.

```python
# data as an array of objects (also what a dataset binding delivers):
[
  { "month": "Jan", "revenue": 42, "cost": 30 },
  { "month": "Feb", "revenue": 51, "cost": 33 }
]
# mapping: { "xColumn": "month", "series": [ {"column": "revenue"}, {"column": "cost"} ] }
# (leave mapping empty and it auto-uses the first non-numeric column as X and every numeric column as a series)
```

## Data & mapping

Every chart accepts an **Ignition dataset** *or* an **array of objects**, so it drops straight onto a
named-query binding or a scripted value. Each family maps columns a little differently:

| Family | Mapping props |
|---|---|
| Line / Area / Column / Bar | `mapping.xColumn` (category) + `mapping.series[]` (`column`, `name`, `color`). Empty = auto. |
| Scatter / Bubble | `mapping.xColumn`, `mapping.series[]` (Y), plus `sizeColumn` (bubble) and optional `labelColumn`. |
| Pie / Donut | `mapping.labelColumn` + `mapping.valueColumn`. |
| Heatmap | `mapping.xColumn`, `mapping.yColumn`, `mapping.valueColumn`. |
| Gauge | no data prop — bind the single **`value`** (plus `min`/`max`/`thresholds`). |
| Sparkline | an array of numbers, or `valueColumn` on an array of objects. |

## Theming & CSS

All styling ships in the module bundle. Axis/grid/label/surface colors follow Perspective's theme
variables (`--neutral-*`, `--container`) so **light/dark is automatic**. The series palette is exposed
as CSS custom properties you can override globally from a project stylesheet:

```css
:root {
  --ecto-chart-1: #0a84ff;   /* series 1 */
  --ecto-chart-2: #ff9f0a;   /* series 2 */
  /* ... up to --ecto-chart-12 ... */
}
```

Everything drawn is a prefixed class you can restyle: `.ecto-chart__line`, `.ecto-chart__area`,
`.ecto-chart__point`, `.ecto-chart__bar`, `.ecto-chart__dot`, `.ecto-chart__slice`,
`.ecto-chart__gauge-value`/`-track`, `.ecto-chart__cell`, plus the shared frame parts
`.ecto-chart__axis`, `.ecto-chart__gridline`, `.ecto-chart__axis-label`, `.ecto-chart__legend*`, and
`.ecto-chart__tooltip*`.

## Common properties

Beyond `data` + `mapping`, the cartesian charts share these (each chart also adds its own block, e.g.
`line.curve`, `bar.thickness`, `donut.innerRatio`, `gauge.thresholds`):

| Prop | Notes |
|---|---|
| `title` | Optional heading above the plot. |
| `categoryAxis` / `valueAxis` | `title`, `showLabels`, `showGridLines`; value axis adds `autoRange` + `min`/`max`. |
| `legend` | `enabled`, `position` (`top`/`bottom`/`left`/`right`). |
| `animation` | `enabled`, `durationMs`. |
| `palette` | Optional list of colors overriding the built-in palette for this chart. |

The Designer property editor shows every prop with inline descriptions, and defaults are chosen so a
freshly-bound chart looks good immediately.

## Control charts (SPC)

The **Control Chart** (`ectobox.chart.control`) is a real SPC chart, not a line chart with two extra
lines on it. Drop it on a view, bind `data`, point `mapping.valueColumn` at your measurement, and you get:

- **Two stacked panels sharing one X axis** — Individuals on top, Moving Range beneath. Every variables
  control chart is a pair, and both panels' limits are computed from the same σ estimate.
- **σ zone bands** — the ±1σ/±2σ/±3σ A/B/C regions filled behind the series, with the A/B/C letters
  in a reserved gutter to the right of the plot rather than printed over your data.
- **Western Electric run rules**, marked on the chart, listed in the hover tooltip, and reported through
  the `violations` property and the `onRuleViolation` event.
- **Correct math.** σ comes from the average moving range (`MR̄ / d2(2)`, i.e. `MR̄ / 1.1284`), not from the
  standard deviation of the individuals — using `s` would let a slow drift inflate the limits until the
  very drift you're hunting looked normal. The d2/d3/c4 factors are the published exact-integral tables,
  so the limits agree with what you'd calculate by hand.

Set **`chartType`** to pick the family. `IMR` (the default) is what you want for one measurement per part
or batch, and is the type this release computes; `XbarR`, `XbarS`, `P`, `NP`, `C`, `U` are in the enum so
that switching later is a property change rather than re-dropping the component.

### Panels and layout

The two panels are configured through `panels`:

| Prop | Notes |
|---|---|
| `panels.primary.title` / `panels.secondary.title` | Panel headings, drawn above each panel. Blank uses the right label for the chart type (“Individual value” / “Moving range” for I-MR). |
| `panels.primary.height` / `panels.secondary.height` | Height *weights* against each other, not pixels — 2 against 1 by default, so the individuals panel gets two thirds. |
| `panels.secondary.show` | Draw the range panel at all. |

Panel headings sit above their panel and the A/B/C zone letters sit in a gutter on the right, so neither
can end up on top of a control limit, a gridline, or the last few points — which is exactly where a
control chart is most worth reading. Both strips are only reserved when there is something to put in
them: turn `zones.labels` off and the plot takes the right-hand gutter back.

In a component too short to render both panels legibly, the range panel **removes itself** rather than
squashing the pair into unreadable strips — an I-MR chart dropped into a 200 px dashboard tile still
shows a usable individuals chart.

### The rules

| id | Rule |
|---|---|
| `beyondLimits` | 1 point beyond zone A (outside the control limits). |
| `twoOfThree` | 2 of 3 consecutive points in zone A or beyond, same side of the centerline. |
| `fourOfFive` | 4 of 5 consecutive points in zone B or beyond, same side. |
| `runOneSide` | 8 consecutive points on one side of the centerline. |

Narrow them with `rules.enabled` (a list of ids); leave it empty to run all four. Runs are never counted
across a gap in the data — "8 in a row" spanning measurements you don't have is a false alarm.

Only `beyondLimits` is applied to the **moving range** panel. A moving range is a skewed statistic whose
consecutive values share an observation, so zone and run rules there fire constantly for no reason.

### Read this bit: `limits.mode` should usually be `frozen`

`compute` is the **default** because it needs no configuration — the limits are recalculated from every
point on the chart. It's the right choice while you're exploring, and the wrong one for a chart somebody
is actually watching:

> With `compute`, a process that drifts out of control **quietly widens its own limits** until nothing
> looks wrong. The excursion you want to catch becomes part of the data the limits are built from.

So in production, take a known-good baseline run and hold those limits:

```python
limits.mode              = "frozen"
limits.baseline.count    = 25       # "the first 25 points"
# or limits.baseline.fromIndex / toIndex for an explicit window
```

`manual` takes limits you supply (`limits.manualPrimary` / `manualSecondary` — a trio counts as set once
`ucl` is above `lcl`, so the all-zero default means "work it out"). σ is re-derived from the limits you
give, so the zone bands still line up with them.

### Read this bit too: spec limits are not control limits

They get conflated constantly, and it matters:

- **UCL / LCL** come from **your process** — what it actually does when nothing is wrong.
- **USL / LSL** come from **your customer's tolerance** — what they'll accept.

A process can be perfectly in control and still produce scrap, or be out of control while every part is
in spec. This component draws **control** limits. Spec limits and the capability numbers (Cp, Cpk, Pp,
Ppk) that compare the two arrive in a later release, and will be drawn distinctly — different dash,
different color, own legend entry — precisely so they can't be mistaken for UCL/LCL.

### Outputs and events

The component writes its own analysis back into two read-only properties, so an exception report is a
binding rather than a script:

| Property | Contents |
|---|---|
| `results` | `inControl`, `pointCount`, `baselineUsed`, `truncated`, and a `primary`/`secondary` block each with `centerline`, `ucl`, `lcl`, `sigma`, `values`. |
| `violations` | One row per rule hit: `index`, `label`, `panel`, `ruleId`, `ruleName`, `description`, `value`. Bind a Table straight to it. |

Writes only happen when the computed answer actually changes, so they can't loop. Set
`output.writeBack = false` to turn them off and drive everything from events instead:

- **`onRuleViolation`** — fires when the set of violations changes, *including back to empty*, so it can
  both raise and clear an alarm. Payload: `violations`, `inControl`, `chartType`.
- **`onPointClick`** — `index`, `label`, `panel`, `value`, and that point's `violations`.

### maxPoints

Historian queries happily return thousands of rows, and an SVG with thousands of markers stops being
usable, so `maxPoints` (default **200**) keeps the most recent N. Truncation is never silent: the title
shows *"showing the last N of M observations"*, the browser console warns, and `results.truncated`
reports it. Note that the limits and rule checks then describe **only the points shown** — set it to `0`
for no cap.

### Styling

Alongside the usual `.ecto-chart__*` classes, the control-chart semantics are their own CSS variables so
a theme can re-skin them: `--ecto-spc-centerline`, `--ecto-spc-limit`, `--ecto-spc-violation`, and
`--ecto-spc-zone-a` / `-zone-b` / `-zone-c`. The centerline is solid and the control limits are dashed on
purpose — you can tell "where the process is centered" from "where it stops being acceptable" without
reading the legend.

## Scripting: `system.ectobox.charts.spc.*`

The same math is available to Jython, for alarming, reports and named-query post-processing — so a
script-driven alarm and the chart on the screen can never disagree. Available in gateway *and* Designer
scope (the Designer registration is what gives you autocomplete while you write).

```python
weights = system.db.runQuery("SELECT fill_weight FROM fills ORDER BY ts")

r = system.ectobox.charts.spc.Analyze(weights, {
        'valueColumn': 'fill_weight',
        'limitMode':   'frozen',
        'baselineCount': 25,
    })

if not r['inControl']:
    for v in r['violations']:
        print "%s at %s: %s (%.2f)" % (v['ruleName'], v['label'], v['panel'], v['value'])
```

| Function | Returns |
|---|---|
| `Analyze(data, options)` | Everything: limits, per-point values/zones/hits, `violations`, `inControl`, `baseline`, `truncated`, `error`. |
| `Limits(data, options)` | Just the centerline/UCL/LCL/σ for each panel — for storing limits or seeding a `manual` chart. |
| `CheckRules(data, options)` | Just the violation list, ready to iterate. |
| `Rules(ruleSet)` | The rules as `id`/`name`/`description` dicts, for building a rule picker or a report. |

`data` accepts a **Dataset** (straight from `system.db.runQuery` or a tag history query), a list of
numbers, a list of dictionaries, or a list of rows. With dictionaries you must set
`options['valueColumn']` — a Jython dict has no column order to guess from.

`options` keys, all optional: `valueColumn`, `labelColumn`, `chartType`, `sigmaMultiplier`, `maxPoints`,
`limitMode`, `baselineCount` / `baselineFrom` / `baselineTo`, `centerline` / `ucl` / `lcl` (and
`mrCenterline` / `mrUcl` / `mrLcl` for the range panel), `ruleSet`, `rules`.

**Always check `r['error']`** before trusting the limits — it's `None` on success, and a sentence
explaining itself when there wasn't enough data or the chart type isn't computed yet.

The factor table is exposed too, so you can build limits by hand without hardcoding `1.128` somewhere:

```python
C = system.ectobox.charts.spc.constants
sigma = mrBar / C.d2(2)          # 1.1284
ucl   = rBar  * C.D4(5)          # 2.1145
```

`d2(n)`, `d3(n)`, `c4(n)`, `D3(n)`, `D4(n)`, `B3(n)`, `B4(n)`, `A2(n)`, `A3(n)` — deliberately keeping
their textbook casing, because that's what every SPC reference and printed factor table calls them.

## Live / streaming data

Because charts re-render on `data` change (and gauges *ease* to new values), a live dashboard needs no
special component — drive any chart from a Perspective **expression binding** on `now(pollRate)` with a
**script transform** that returns fresh data each tick:

```
Binding:   now(1000)                 # re-evaluates every second
Transform (script):
    import math
    t = value.getTime() / 1000.0     # now() returns a Date — use getTime() for millis
    return int(round(55 + 35 * math.sin(t / 5.0)))
```

Gauges glide between values; set `animation.enabled = false` on rolling line/area/sparklines so they
update like a clean live monitor.

<img width="1016" height="913" alt="msedge_vwl2qnCWWL" src="assets/Charts-4.gif" />
