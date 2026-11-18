# Layout recipes for report and paper figures

Three compositions cover most paper figures. Pick by the relationship, not by taste: bands for containment, columns for a pipeline, cards for a graph of relationships.

All three use `@startdot` (raw Graphviz inside a `.puml`). That is not a preference — PlantUML's own node labels cannot express these layouts:

- `rectangle "<TABLE ...>"` fails outright on a multi-line table (`Error line N`) and renders a single-line table as literal text.
- HTML table cells cannot be rounded by `skinparam`; only Graphviz cell styling rounds them.
- `dot` will not place nodes at fixed coordinates under `@startdot`, so hand-computed positions are not an option: the table *is* the layout.

Cost: `@startdot` needs a local Graphviz on `PATH` (`dot -V`; tested 2.44.1), and PlantUML suppresses SVG for this diagram type — `-tsvg` writes a 340-byte "This feature has been suppressed" stub. Produce the SVG by stripping the `@startdot`/`@enddot` lines and running `dot -Tsvg`; its PNG is byte-comparable in size to the jar's (both 1819x810 on the same source).

## Shared primitives

Every hex below is a placeholder. No palette is mandated by this skill: take the colours from the project's own style guide and substitute them here, keeping the roles — one tint for a band, one fill for its chips, one accent for the band header. Roles, not values, are what the recipes depend on.

```dot
node [shape=box, style="rounded,filled", penwidth=1.2,
      color="#4C9FD3", fillcolor="#E9F4FD", fontname="Microsoft YaHei", margin="0,0"];
```

- Band or column panel: a node with `style="rounded,filled"`, tint fill, colored border, and an HTML-table label.
- Chip inside a table: `<TD BGCOLOR="#2685BE" STYLE="ROUNDED"><FONT COLOR="#FFFFFF">…</FONT></TD>`.
- Optional or terminal chip: `STYLE="ROUNDED,DASHED"` with a light fill and dark text.
- Never put the figure title inside the image when the report supplies a caption.
- Roles: one hue per band in a layered figure; one hue for the whole pipeline in a stage figure, with an accent for decisions and a muted one for failure exits. Assign lightness so the bands stay distinguishable when printed without colour; do not rely on hue alone.
- Placeholder fills used in the snippets below measure about 3.2–4.1:1 against white text, which is below the 4.5:1 needed at 17 pt. Treat them as geometry examples: pick fills that pass, or switch to dark text on light fills.

## A. Layered bands (containment)

Use when the reader must learn what belongs to what: system architecture, research framework, capability stack.

Each band is one node; bands are chained with invisible edges so they stack, and every band's inner table is built with the same label width and content width so the borders line up:

```dot
@startdot
digraph Architecture {
graph [bgcolor="white", margin=0, pad=0.12, rankdir=TB, nodesep=0, ranksep=0.26];
node [shape=box, style="rounded,filled", penwidth=1.3, fontname="Microsoft YaHei", margin="0,0"];
edge [style=invis];
b1 [fillcolor="#E7F4F7", color="#3795A6", label=<
<TABLE BORDER="0" CELLBORDER="0" CELLSPACING="0" CELLPADDING="14"><TR>
<TD WIDTH="168" ALIGN="LEFT" VALIGN="MIDDLE"><FONT POINT-SIZE="21"><B>表示层</B></FONT></TD>
<TD WIDTH="1104" ALIGN="LEFT" VALIGN="MIDDLE">
  <TABLE BORDER="0" CELLBORDER="0" CELLSPACING="0" CELLPADDING="0"><TR>
  <TD WIDTH="340" HEIGHT="50" BGCOLOR="#24899A" ALIGN="CENTER" BORDER="0" STYLE="ROUNDED"><FONT COLOR="#FFFFFF" POINT-SIZE="17">页面</FONT></TD>
  </TR></TABLE>
</TD></TR></TABLE>>];
b1 -> b2;
}
@enddot
```

Keep `W_LABEL` and total content width identical across bands; otherwise the widest band stretches the others and the left labels stop aligning.

## B. Stage columns (pipeline)

Use when the reader must follow a procedure: experiment workflow, request handling, data processing. Horizontal columns replace a tall vertical activity diagram, which rarely fits a page.

Build one table with a single row of panels, separated by gap cells carrying the arrow glyph:

- Each panel: `TD BORDER="1" COLOR="…" BGCOLOR="…" VALIGN="TOP" CELLPADDING="12" STYLE="ROUNDED"` holding a nested table of chips.
- Panel heights equalise by themselves (one table row), headers stay aligned at the top, and content stays top-aligned via `VALIGN="TOP"`.
- Between panels: `<TD WIDTH="44" ALIGN="CENTER" VALIGN="MIDDLE"><FONT POINT-SIZE="24">→</FONT></TD>`.
- Inside a panel, a small `↓` cell between chips marks sequence; label decision exits `是`/`否` in the same cell style.
- Failure or rejection branches end in a dashed muted chip inside the panel that owns the decision. Do not draw a separate end node per stage — readers then think every stage terminates.

## C. Cards with orthogonal links (relationships)

Use when the content is a graph: entities, states, roles. Plain PlantUML does this well; it needs consistency, not a new layout engine:

- One card style for all nodes, one line width for all edges, labels on edges rather than in node bodies.
- Keep the arrow count down. If several edges share a source and a target, merge them into one labelled edge.
- Put caveats in the surrounding text. A figure carrying a footnote box competes with its own content.

## Legibility check, with numbers

A figure is only as readable as its smallest text after scaling to the target column. Measured rule, verified on the band and stage figures above:

- printed text size ≈ `text_px / figure_px × 453.6 pt` for a 16 cm column.
- So 8 pt needs `text_px / figure_px ≥ 1.8%`; 17 pt text therefore caps the figure at about 1260 px wide.
- A 1987 px stage figure at 16 cm gives 5.2 pt — legible on screen, marginal in print. When it lands below the threshold, split the pipeline into two rows or drop a column; do not shrink the type.

Always open the rendered image, and check the pixel size before delivering. Rendering without error only proves the syntax parsed.
