# HTML Report Format

The folder-architecture review is rendered as a single self-contained HTML file in the OS temp directory. Use the same visual system as `improve-codebase-architecture`: Tailwind and Mermaid come from CDNs; Mermaid handles graph-shaped diagrams; hand-built divs and inline SVG handle editorial tree, path, and locality visuals. Copy this scaffold and style language for every run. Change repository content, not the layout system.

## Scaffold

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Folder architecture review — {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* small custom layer for dashed nesting/seam lines and editorial diagrams */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
      .path-hit { background: #d1fae5; border-color: #059669; }
      .path-jump { color: #dc2626; }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

## Header

Show the repository name, review date, and a compact legend:

- solid box = folder/module path;
- dashed line = nesting or proposed seam;
- red arrow = a cross-tree jump, leaked concept, or dependency mismatch;
- thick dark box = a path with strong locality and leverage;
- green highlight = the representative change path being traced.

Start directly with the orientation and candidates. Keep the header compact; do not add a long introduction paragraph.

## Candidate card

Render each candidate as one `<article>` with this order:

1. **Title** — short, imperative name for the structural move, such as “Group the payment flow by capability.”
2. **Badge row** — recommendation strength (`Strong` = emerald, `Worth exploring` = amber, `Speculative` = slate), plus the structural decision category (`capability`, `technical role`, `hybrid`, `naming`, or `orientation`).
3. **Files / paths** — exact paths in `font-mono text-sm`.
4. **Before / After diagram** — centrepiece, two columns, same representative change path in both.
5. **Problem** — one sentence describing the observed cold-start or change-locality friction.
6. **Proposal** — one sentence describing the smallest structural change.
7. **Wins** — bullets, six words or fewer when possible. Examples: “Locality: one capability path”, “Fewer cross-tree jumps”, “Tests follow the behavior”, “Names reveal ownership”.
8. **Evidence** — a compact list of observed paths and the representative task used.
9. **Trade-offs / validation** — one compact block naming migration cost, new ambiguity, and the falsifiable task that would validate the candidate.
10. **ADR callout** — amber-tinted one-line warning only when an existing ADR is materially contradicted.

Diagrams carry the weight. Keep prose sparse, plain, and evidence-backed. If a diagram needs a paragraph to be understood, redraw the diagram.

## Diagram patterns

Pick the pattern that fits the candidate. Vary patterns across candidates, but keep the visual grammar fixed.

### Tree path comparison

Use hand-built boxes and thin connectors for “where would I look?” Before: the highlighted task path jumps between technical folders. After: the same path is contiguous or has one clearly labeled seam. Show enough surrounding context to make the decision legible.

```html
<div class="grid md:grid-cols-2 gap-4">
  <div class="rounded-lg border border-slate-200 bg-white p-4">
    <p class="text-xs uppercase tracking-wider text-slate-500">Before · scattered path</p>
    <div class="mt-4 space-y-2 font-mono text-sm">
      <div class="rounded border p-2 path-hit">routes/checkout</div>
      <div class="text-center path-jump">↓ jump</div>
      <div class="rounded border p-2">domain/payments</div>
      <div class="text-center path-jump">↓ jump</div>
      <div class="rounded border p-2">tests/integration</div>
    </div>
  </div>
  <div class="deep rounded-lg p-4 text-white">
    <p class="text-xs uppercase tracking-wider text-slate-300">After · local path</p>
    <div class="mt-4 space-y-2 font-mono text-sm">
      <div class="rounded border border-emerald-300 p-2">checkout/</div>
      <div class="ml-4 rounded border border-emerald-300 p-2">payment-flow/</div>
      <div class="ml-8 rounded border border-emerald-300 p-2">implementation + tests</div>
    </div>
  </div>
</div>
```

### Mermaid dependency / change flow

Use Mermaid when the point is “the change crosses these branches.” Name paths, not generic boxes. Use red edges for leakage or cross-tree jumps and a dark class for a cohesive destination.

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[routes/checkout] --> B[domain/payments]
      B -.cross-tree jump.-> C[tests/integration]
      B --> D[adapters/gateway]
      classDef leak stroke:#dc2626,stroke-width:2px;
      classDef deep fill:#0f172a,color:#fff,stroke:#0f172a;
      class B,D leak;
  </pre>
</div>
```

### Nesting-depth cross-section

Use horizontal bands to show levels that add no useful distinction. Before: many thin bands with repeated technical labels. After: fewer levels, each answering a different navigation question. Do not treat fewer folders as automatically better.

### Change-coupling map

Use hand-built nodes or Mermaid to show the files touched by one representative bug fix or feature. Before: red edges fan across unrelated top-level branches. After: the same task sits inside one capability path with only explicit adapters or shared modules outside it.

### Orientation surface

Use an editorial panel when the candidate is a missing README, map, ownership note, or local convention. Show the cold-start question on the left and the proposed answer path on the right. The artifact is an orientation aid, not a substitute for a misleading tree.

## Style guidance

- Keep the editorial style from `improve-codebase-architecture`: generous whitespace, stone/slate palette, optional serif headings, one accent plus red leakage and amber warnings.
- Keep diagrams around 320px tall so before/after fits side by side without scrolling.
- Use `text-xs uppercase tracking-wider` for schematic labels.
- Use solid boxes for paths and thick dark boxes for high-locality proposed groups; do not use gradients or color to imply correctness beyond the fixed legend.
- Use the same border radii, spacing scale, type hierarchy, and badge colors in every report.
- The only scripts are the Tailwind CDN and Mermaid ESM import. The report is otherwise static.
- If CDN rendering fails, the HTML must still show the headings, paths, prose, and text equivalents.

## Top recommendation section

Render one larger card containing the candidate name, one sentence on why it is first, the strongest counterargument, and an anchor link to its card. Keep it concise.

## Tone and vocabulary

Use plain English, concise evidence, and provisional recommendations. Use these terms consistently: **module**, **interface**, **implementation**, **depth**, **deep**, **shallow**, **seam**, **adapter**, **leverage**, **locality**. For folder architecture, explain that a path acts as a navigational interface; do not call it a component, service, unit, API, signature, or boundary.

Say:

- “The checkout path is scattered across three technical branches.”
- “This candidate increases locality for the payment change, but introduces a second navigation rule.”
- “The proposed folder is shallow if it only renames an existing path.”
- “Validate with a bug fix and a new capability task.”

Avoid:

- “This is the cleanest structure.”
- “Move everything into features.”
- “The architecture is wrong.”
- generic dashboard copy such as “improves maintainability.”

## Acceptance checklist

Before opening the report, verify:

- it uses the exact scaffold sections and fixed visual language;
- every candidate is an `<article>` with all required fields in the specified order;
- every before/after diagram traces the same representative task;
- color and spacing match this specification;
- facts, inferences, trade-offs, and unresolved decisions are labeled;
- the top recommendation links to its candidate card;
- the report ends with the candidate-selection prompt.
