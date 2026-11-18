---
name: silver-plantuml
description: Create PlantUML flowchart diagrams (`.puml` / `.plantuml`) and render them to images via a local `plantuml.jar`. Use when the user asks for a flowchart, flow diagram, process diagram, decision tree, or any step-by-step visualization with branches or conditions.
---

# PlantUML Flowchart

## Rendering

The jar is bundled in this skill directory as `plantuml-1.2026.6.jar`. Resolve it against the skill directory path Pi reports for this skill — not against the workspace, and not against any hardcoded user path:

```bash
java -jar "<skill-dir>/plantuml-1.2026.6.jar" -DPI=300 <file>.puml
```

Writes `<file>.png` next to the source. **DPI must be ≥ 300 for PNG output — never render below 300.** Use `-tsvg` for SVG (resolution-independent, no DPI concerns).

## Syntax Conventions

- Use `flowchart TB | LR | BT | RL`. Prefer `flowchart` over the older `graph` keyword.
- Wrap the source in `@startuml` / `@enduml` tags so PlantUML renders it.
- One diagram per `.puml` file. **Save `.puml` files in the user's project folder, never in the skill folder.** The skill folder is reserved for `SKILL.md` and skill assets only.
