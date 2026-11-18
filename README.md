# silver-plantuml

A skill for the Pi coding agent that renders `.puml` sources to images with a bundled
PlantUML jar.

## Contents

| File | Purpose |
|---|---|
| `SKILL.md` | The instructions Pi loads: the render command and the PlantUML syntax that actually works |
| `plantuml-1.2026.10.jar` | PlantUML 1.2026.10, unmodified upstream release, GPL-3.0-or-later |
| `NOTICE.md` | Licenses and attribution for the bundled jar |

## Requirements

- Java 21 or newer on `PATH` (verified on Java 22; the jar contains class files up to
  major version 65)

## Install

Put this directory where Pi scans for skills:

| Scope | Location |
|---|---|
| User | `~/.pi/agent/skills/silver-plantuml/` |
| Agent skills (user) | `~/.agents/skills/silver-plantuml/` |
| Agent skills (project) | `.agents/skills/silver-plantuml/` |

To keep one copy instead of a copy per location, link it — for example on Windows:

```powershell
New-Item -ItemType Junction `
  -Path "$env:USERPROFILE\.pi\agent\skills\silver-plantuml" `
  -Target "<path-to-this-repo>"
```

Once installed, Pi discovers the skill by its description, or you can force it with
`/skill:silver-plantuml`.

## Usage outside Pi

`SKILL.md` resolves the jar against the skill directory, so no absolute path is embedded
and the jar can live wherever this repository is checked out:

```bash
java -jar plantuml-1.2026.10.jar diagram.puml        # writes diagram.png next to the source
java -jar plantuml-1.2026.10.jar -tsvg diagram.puml  # writes diagram.svg
```

Two behaviours worth knowing:

- `-DPI` is silently ignored by this CLI build. `-DPI=100` and `-DPI=300` produce
  byte-identical PNGs. Emit SVG when the output has to survive zooming or printing.
- PlantUML is not Mermaid. `flowchart TB`, `graph TB` and `A[label]` fail with
  `Error line N`; use `A --> B` links or the activity syntax shown in `SKILL.md`.

## Licensing

The bundled jar is PlantUML, GPL-3.0-or-later, redistributed unmodified together with all
of its own license notices. See `NOTICE.md`.
