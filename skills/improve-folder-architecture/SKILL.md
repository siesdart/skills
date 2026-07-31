---
name: improve-folder-architecture
description: Review a project's folder architecture and produce evidence-backed structural candidates in a visual HTML report.
disable-model-invocation: true
---

# Improve Folder Architecture

Assess the folder tree as a navigational interface, not as a style contest. Help a cold-start developer answer:

1. Where does the main product flow begin and end?
2. Where should a change for a named capability live?
3. What else is likely to change, and what should remain untouched?

Use the `codebase-design` vocabulary precisely: **module**, **interface**, **depth**, **seam**, **adapter**, **leverage**, and **locality**. Diagnose and propose candidates; implementation is a later phase.

Read [HTML-REPORT.md](HTML-REPORT.md) before creating the report. It is the single source of truth for the scaffold, visual language, card structure, diagrams, and tone.

Use [EVALUATION-LENSES.md](EVALUATION-LENSES.md) when comparing structures or deciding whether a convention earns its place.

## 1. Establish scope and evidence

Identify the repository root and inspect the available evidence:

- top-level folders and files, including hidden configuration and manifests;
- README and contributor/developer documentation;
- build, test, lint, format, and deployment entry points;
- ADRs, `CONTEXT.md`, ownership files, and local agent instructions;
- recent history with `git log --oneline --decorate -n 80` and path statistics when useful.

Exclude generated, vendored, cache, build, dependency, and coverage directories from product architecture, while recording them as tooling evidence.

Trace representative paths through an entry point, main product flow, tests, configuration, persistence or external adapters, and documentation. Do not infer meaning from folder names alone. When routes, commands, screens, jobs, or other public entry points organize the tree, map each to its implementation path and test whether that path is a useful ownership interface.

If the workspace lacks a repository, source tree, manifest, documentation, and Git history, stop diagnosis. Report the inspected scope and request the repository path or files; do not fabricate candidates.

Completion criterion: the inventory names the visible areas, entry points, conventions, exclusions, and at least two verified end-to-end change paths.

## 2. Explore the developer journey

Explore recent hotspots and the paths needed to understand the main flow. Use an Explore subagent for an independent pass when available, giving it the repository and user-facing objective rather than a suspected answer.

Simulate a cold-start developer and an agent. For a named capability and for one representative request, command, job, screen, or domain flow, record:

- the first trustworthy orientation point;
- the implementation and test paths;
- likely files for a small bug fix and a new cross-cutting feature;
- folder jumps, ambiguous names, duplicate concepts, unexplained nesting, and hidden conventions;
- whether tests and documentation are discoverable through a predictable rule.

Measure friction with concrete paths and tasks. Evaluate a convention through its complete use in the system. Preserve entry-point locality and stable order-encoding when they help a reader follow ownership, composition, execution, or lifecycle; propose change only when a representative task shows ambiguity, drift, or materially worse locality.

For every finding, separate observed facts from inferences and include a confidence level. Apply the deletion test: a folder earns its place when it hides meaningful complexity, provides a stable seam, or creates useful locality and leverage for a coherent capability.

Completion criterion: every finding has a concrete path, representative change journey, observed friction, fact/inference label, and confidence level.

## 3. Generate and rank candidates

Generate 3–5 materially different candidates, or record “no structural change” when the evidence supports it. Candidates may address:

- capability, product-area, deployment, or technical-role grouping;
- nesting depth and names that reveal intent, ownership, lifecycle, or runtime role;
- placement of tests, fixtures, configuration, documentation, and adapters;
- competing organizational schemes or missing orientation artifacts;
- dependency direction and visibility of seams;
- entry-point paths that should be preserved or made more explicit.

Compare candidates against repository scale, change patterns, ownership, runtime boundaries, tooling, and existing conventions. Do not prescribe capability grouping, flatness, or depth universally. Keep code with its route, command, screen, or job when that path is the clearest ownership interface; extract shared code only when verified coordination outweighs lost entry-point locality.

For each candidate, state:

- **Files/paths** — exact folders and representative files;
- **Evidence** — what a cold-start developer does today;
- **Problem** — the lost discoverability, locality, leverage, or dependency clarity;
- **Proposal** — the smallest structural change;
- **Trade-offs** — migration cost, ambiguity, coupling, tooling impact, and intentional non-goals;
- **Validation** — a falsifiable bug-fix or feature task;
- **Strength** — `Strong`, `Worth exploring`, or `Speculative`.

Rank by expected improvement in cold-start comprehension and change locality, adjusted for migration risk and evidence strength. Use no numeric score unless requested.

Completion criterion: every candidate represents a distinct decision, includes evidence, trade-offs, and a falsifiable validation, and can be rejected independently.

## 4. Present the candidates

Write a fresh report to the OS temporary directory as `folder-architecture-review-<timestamp>.html`. Resolve the directory from `$TMPDIR`, falling back to `/tmp` on Unix or `%TEMP%` on Windows. Do not write it into the repository. Open it with the platform default command when permitted and report the absolute path.

Follow [HTML-REPORT.md](HTML-REPORT.md) exactly. Include its fixed header, legend, candidate-card order, before/after visualization for every candidate, and top-recommendation section. Label observed state, proposed state, unresolved questions, and subjective decisions. Keep implementation deferred.

End the report and assistant message with: **Which of these would you like to explore?**

Completion criterion: the report opens successfully, contains all candidates and their evidence, names one top recommendation with rationale, links to that candidate, and ends with the selection prompt.

## 5. Grill the selected candidate

When the user selects a candidate, invoke the `grilling` skill. Ask one decision question at a time and give a recommended answer with rationale. Explore in this order:

1. user journey and change types;
2. runtime, tooling, ownership, release, and migration constraints;
3. the semantic unit that owns the capability;
4. seam and dependency direction;
5. names and nesting alternatives;
6. tests, fixtures, documentation, configuration, and adapters;
7. incremental migration and rollback;
8. cold-start and representative-change validation.

If the design introduces a domain term, use `domain-modeling` to update `CONTEXT.md`. Offer an ADR only when a durable decision would prevent meaningful future re-litigation.

Completion criterion: the user has confirmed the shape, trade-offs, migration slice, and validation plan. Only then may implementation begin.

## Guardrails

- Keep the evaluation technology-agnostic; mention framework conventions only as repository evidence or constraints.
- Prefer the smallest useful structural change. Preserve existing user changes and do not modify source files during exploration.
- Treat paths as interfaces and the tree as a navigational interface; judge depth and order by reader leverage and maintainer locality.
- Keep ownership and coordination costs explicit whenever code crosses an entry-point seam.
- Surface ADR conflicts only when observed friction justifies reopening the decision.
