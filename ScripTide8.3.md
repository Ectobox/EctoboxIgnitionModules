# ScripTide for Ignition 8.3

**⬇ [Download the latest module (v1.0.8)](https://github.com/Ectobox/EctoboxIgnitionModules/releases/download/modules/Script-IDE-8.3.modl)**

ScripTide is a free Ignition Designer module that replaces the built-in Script Console with a full-featured script IDE. If you've ever found yourself wishing the Script Console had tabs, autocomplete, or a way to save your work, this is the module for you.

It's been built by a small team and shaped largely by feedback from real users. We're genuinely grateful for anyone who takes the time to try it, report an issue, or suggest an improvement.

---

## Table of Contents

- [Installation](#installation)
- [Opening ScripTide](#opening-scriptide)
- [Editor & Tabs](#editor--tabs)
- [IntelliSense / Autocomplete](#intellisense--autocomplete)
- [Output: cprint & jsonPrint](#output-cprint--jsonprint)
- [Script Library](#script-library)
- [Snippets](#snippets)
- [Execution History](#execution-history)
- [Tests (IgniTest)](#tests-ignitest)
- [Import & Export](#import--export)
- [Hotkeys](#hotkeys)
- [Font Settings](#font-settings)
- [Themes](#themes)
- [Data Storage](#data-storage)
- [Contributing & Feedback](#contributing--feedback)

---

## Installation

1. Download the latest `Script-IDE.modl` from the [Releases](../../releases) page.
2. In the Ignition Gateway, go to **Config → Modules** and click **Install or Upgrade a Module**.
3. Upload the `.modl` file. On a fresh install you'll be prompted to accept the certificate.
4. Restart the Designer if it was already open.

No Gateway configuration is required. ScripTide is a Designer-only module and has no runtime component.

---

## Opening ScripTide

Once installed, ScripTide can be opened two ways:

- **Tools menu → Script IDE**
- **F12** from anywhere in the Designer

<img width="356" height="115" alt="image" src="https://github.com/user-attachments/assets/62d23e1b-6285-4305-9d8c-95cf5da9e36c" />

---

## Editor & Tabs

The editor supports multiple tabs so you can keep several scripts open at once. Tabs persist between Designer sessions, so you won't lose your work if you close and reopen the Designer.

Each tab gets its own execution scope, run history, and undo stack. 

<img width="359" height="112" alt="image" src="https://github.com/user-attachments/assets/01928a0e-8f84-4a83-80b4-2f09ea8b7dfe" />

The editor supports full JPython syntax highlighting, bracket matching, and real-time syntax validation that underlines errors as you type.

<img width="467" height="182" alt="image" src="https://github.com/user-attachments/assets/d074c35c-f26a-4779-a1f7-af8422ebb64f" />

<img width="547" height="157" alt="image" src="https://github.com/user-attachments/assets/4b05a622-a568-4af4-9eb2-f68bd3a3389b" />

---

## IntelliSense / Autocomplete

ScripTide includes completion support for the full Ignition `system.*` API, Python built-ins, your project's script resources, local variables in the current script, snippet triggers, and the IgniTest testing framework.

Completions are scope-aware — for example, `system.gui.*` functions won't appear when you're running in Gateway scope, and `system.perspective.*` won't appear in a Vision Client context.

Script resource completions work hierarchically. If your project has a script module at `Ectobox.Database`, typing `Ectobox.Database.` will offer completions for the functions defined inside it.

<img width="914" height="405" alt="image" src="https://github.com/user-attachments/assets/ff217444-6970-4e62-877a-b7f5894f8d05" />

---

## Output: cprint & jsonPrint

The output panel supports ANSI color, and ScripTide ships two helper functions you can use directly in your scripts.

### cprint

`cprint` lets you write colorized output using a simple markup syntax:

```python
cprint("[green]Success:[/] All tags updated.")
cprint("[bold][red]Error:[/] Tag path not found.")
cprint("[#FF8800]Custom orange text[/]")
```

Supported tags include named colors (`red`, `green`, `blue`, `cyan`, `yellow`, `magenta`, `white`), bright variants (`bright_red`, etc.), and formatting (`bold`, `italic`, `underline`, `strike`). Hex color codes like `[#RRGGBB]` are also supported. Closing tags (`[/]`) are added automatically if you forget them.

<img width="1179" height="265" alt="image" src="https://github.com/user-attachments/assets/2bf0acd5-4a55-4620-ad4c-e69ff8a844e5" />

### jsonPrint

`jsonPrint` pretty-prints JSON with syntax highlighting. It accepts either a JSON string or a Python dict or list:

```python
jsonPrint(Ectobox.Data.Lookups.SelectCountries())
jsonPrint('{"status": "ok", "count": 42}')
```

Keys are shown in cyan, strings in green, numbers in magenta, booleans in yellow, and nulls in gray. If the input isn't valid JSON, the error is shown with line and column information.

<img width="668" height="436" alt="image" src="https://github.com/user-attachments/assets/db2af89b-0b5d-4d5a-95b9-1b2735df1b14" />

---

## Script Library

The Script Library lets you save, organize, and reload scripts. It's split into two scopes — **Global** (available in any project) and **Project** (scoped to the current Ignition project).

Within each scope, scripts can be organized into folders. You can rename scripts and folders, and mark scripts as favorites (they'll show a ★ in the tree).

Double-click any saved script to load it into the current tab.

<img width="221" height="233" alt="image" src="https://github.com/user-attachments/assets/202aceaa-260a-4eea-96ee-2d17c7183b7d" />

### Version History

Every time you save a script, ScripTide keeps up to 50 previous versions. You can open the version history for any saved script to browse, preview, and restore earlier versions.

<img width="824" height="493" alt="image" src="https://github.com/user-attachments/assets/1537742a-0a2a-4507-ae90-0b6309dd7ddd" />

---

## Snippets

The Snippet Library stores reusable pieces of code. Like the Script Library, snippets are organized by Global and Project scope, with optional categories.

Snippets support `${placeholder}` syntax for template variables. When a snippet is inserted, placeholders are removed so you can fill them in — or you can define them to act as hints in the preview.

You can assign a **trigger word** to a snippet. Typing that word in the editor and pressing Tab will expand it inline — similar to IDE live templates.

<img width="366" height="874" alt="image" src="https://github.com/user-attachments/assets/d685ac2a-ae64-48d6-b01a-ac52bd28bb25" />

---

## Execution History

Every script run is logged to the History panel, including the script content, the scope it was run in, and the timestamp. You can browse your history, preview past scripts, and load any of them back into the editor.

<img width="598" height="421" alt="image" src="https://github.com/user-attachments/assets/9f538d60-7b32-480f-b56b-8db981f1f968" />

---

## Tests (IgniTest)

ScripTide includes a lightweight Python testing framework called **IgniTest**. Test scripts live in the Test Library and can be run directly from ScripTide.

A basic test file looks like this:

```python
from ignitest import *

@Test("Tag read returns expected value")
def test_tag_value():
    with MockTagRead("[default]MyTag", 42):
        value = system.tag.readBlocking(["[default]MyTag"])[0].value
        AssertEquals(value, 42)

@Test("Database row count")
def test_db():
    with MockDbQuery("SELECT * FROM devices", [["PLC1"]], ["name"]):
        ds = system.db.runQuery("SELECT * FROM devices")
        AssertDbRowCount(ds, 1)
```

**Decorators:** `@Test`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`, `@Skip`, `@Timeout`, `@TestCase`

**Assertions:** `AssertEquals`, `AssertNotEquals`, `AssertTrue`, `AssertFalse`, `AssertNone`, `AssertNotNone`, `AssertIn`, `AssertNotIn`, `AssertThrows`, `AssertGreaterThan`, `AssertLessThan`, `AssertAlmostEquals`, `AssertTagValue`, `AssertDbRowCount`

**Mocks:** `MockTagRead` and `MockDbQuery` are context managers that intercept `system.tag` and `system.db` calls so your tests don't need a live system.

Results are shown in the output panel with pass/fail counts and any failure details.

<img width="601" height="433" alt="image" src="https://github.com/user-attachments/assets/89444e47-b4cf-4307-8482-6f88c5b11c26" />

---

## Import & Export

You can export scripts, snippets, and tests to a portable `.json` bundle file and import them into any other project or Ignition installation. This is useful for sharing libraries between teams or keeping backups outside of version control.

When importing, you can preview what's in the bundle, choose which items to bring in, and pick whether they should go into the Global scope or the current Project.

<img width="886" height="593" alt="image" src="https://github.com/user-attachments/assets/049b9834-d9c0-4132-ace3-e392e968d327" />

<img width="886" height="593" alt="image" src="https://github.com/user-attachments/assets/52c289d1-bbae-4265-8908-f5b59ce2f586" />
---

## Hotkeys

Most actions in ScripTide have a keyboard shortcut. All shortcuts can be customized from **Settings → Keybindings**.

| Action | Default |
|---|---|
| Run Script | Ctrl+Enter |
| Stop Script | Ctrl+. |
| Save Script | Ctrl+S |
| Find | Ctrl+F |
| Find & Replace | Ctrl+H |
| New Tab | Ctrl+N |
| Close Tab | Ctrl+W |
| Organize Imports | Ctrl+Shift+O |
| Increase Font Size | Ctrl+= |
| Decrease Font Size | Ctrl+- |
| Reset Font Size | Ctrl+0 |

To rebind a shortcut, double-click the action in the Keybindings dialog and press your new key combination. ScripTide will warn you if the combination is already in use.

<img width="886" height="593" alt="image" src="https://github.com/user-attachments/assets/ac17c63f-e2a6-4851-a2ff-ca2c3a2c9452" />

---

## Font Settings

The font can be configured separately for different parts of the IDE — menu items, buttons, the editor, and the output panel. You can choose any font installed on your system, set the size, and toggle bold or italic.

A live preview updates as you make changes so you can see exactly what you'll get before committing.

Font settings are accessed from **Settings → Fonts**.

<img width="886" height="593" alt="image" src="https://github.com/user-attachments/assets/33efc40b-21cc-4e0f-bfd3-8021a74c0bdc" />

---

## Themes

ScripTide includes a light theme and a dark theme, and a theme editor that lets you create custom themes by adjusting colors to your liking.

> **Note:** The theming system is still being refined and has some rough edges. For now, we'd recommend sticking with the **default light theme** for the most stable experience. The dark theme and custom themes are available to try, but may have visual inconsistencies in some areas. We're actively working on improvements.

<img width="789" height="238" alt="image" src="https://github.com/user-attachments/assets/539f982f-a5de-4291-9797-bb6c68313d10" />

---

## Contributing & Feedback

ScripTide is a passion project and we really appreciate users taking the time to try it out. If you run into a bug, have a feature suggestion, or just want to share how you're using it, please open an issue on GitHub — it genuinely helps shape where the project goes.

Thank you for using ScripTide.
