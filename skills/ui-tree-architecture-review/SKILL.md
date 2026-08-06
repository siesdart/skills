---
name: ui-tree-architecture-review
description: Review a frontend application's rendered component or widget tree from the top-level entry point downward. Use when assessing whether UI nodes are cohesive, appropriately split, easy to change and test, and connected by clear technology-neutral contracts such as inputs, outputs, events, slots, bindings, or shared context.
disable-model-invocation: true
---

# UI Tree Architecture Review

Review the UI tree as a set of ownership and change boundaries. The goal is not a preferred framework pattern or a target number of components; it is a tree whose nodes hide meaningful complexity, expose understandable contracts, and keep likely changes local.

Read [REPORT-TEMPLATE.md](REPORT-TEMPLATE.md) before writing the report. It is the single source of truth for the output structure. Use the terms **node**, **parent**, **child**, **boundary**, **contract**, **state owner**, **composition**, **locality**, and **leverage** consistently across technologies.

## 1. Establish scope and evidence

Identify the application entry points and the user-visible tree or trees in scope. Inspect the source files that construct them, the node definitions, styles/layout rules, state and data sources, tests, fixtures, story/demo files, routing, and relevant project instructions. Use repository-native commands and conventions to discover files; do not infer architecture from filenames alone.

Trace each selected tree top-down. For every node, record its parent, children, rendered responsibility, inputs, outputs/events, state read or owned, external effects, styling/layout authority, and tests or isolated examples. Distinguish observed facts from inferences. If the tree cannot be reconstructed from available evidence, report the missing evidence and stop rather than inventing structure.

Completion criterion: the scope names every inspected entry point and selected tree, and the inventory accounts for each visible node's implementation, contract, state/effect source, and available verification evidence.

## 2. Map the change axes

For the main user flows, simulate representative changes: a visual-only change, a behavior change, a state or data change, an accessibility change, and a cross-cutting change. Follow the files and contracts each change touches. Note repeated edits, prop/event forwarding, hidden context, duplicated state, parent knowledge of child internals, and changes that cross unrelated responsibilities.

Use the **deletion test** for every proposed boundary: if removing the node merely moves the same coordination and knowledge into its parent, the node is shallow; if it hides policy, layout, state transition, adaptation, lifecycle, or a meaningful variation behind a stable contract, it may be earning its keep. Use the **change-axis test**: nodes are stronger when the reasons to change inside them travel together and likely changes outside them do not.

Completion criterion: each finding is tied to at least one concrete change path and states which observed files, contracts, or runtime behaviors create the claimed friction.

## 3. Evaluate every boundary

Apply these lenses in order. A later lens can refine a conclusion, but should not override a clear ownership or contract problem without evidence.

1. **User-facing responsibility and cohesion** — Does the node have one understandable purpose at its level of the tree? Keep visual details, interaction policy, domain policy, and orchestration together only when they change together. A small node is not automatically a good node, and a large node is not automatically a problem.
2. **State and effect ownership** — Is each state value owned at the lowest node that can maintain its invariant while serving all consumers? Lift it only for a demonstrated shared need. Keep data fetching, subscriptions, mutations, timers, and other effects at the boundary that owns their lifecycle or policy.
3. **Contract depth and direction** — Are inputs, outputs/events, slots/content, bindings, and shared context named in terms of the consumer's intent? Prefer a small contract that hides meaningful decisions. Flag pass-through values, callback plumbing, ambiguous bags, bidirectional mutation, and child access that expose implementation details. Check that information and control flow in the contract match the intended ownership direction.
4. **Composition and variation** — Can the parent compose the child without knowing its internal structure? Prefer explicit composition points for genuine variation. Treat excessive configuration flags, conditional branches for unrelated modes, and render/content escape hatches as evidence to investigate, not automatic violations.
5. **Locality and change isolation** — Does a likely change remain near the node that owns it? Check styling and layout leaks, duplicated responsive rules, shared selectors, global assumptions, and broad context dependencies. Preserve a boundary when it creates useful locality even if it is not reused elsewhere.
6. **Independent verification and DX** — Can a developer understand, render, test, debug, and document the node with a manageable setup? Check isolated states, loading/error/empty/disabled/focus states, accessibility behavior, mocks or fixtures, discoverable names, and the cost of tracing through indirection. Isolation is evidence of a useful seam, not a mandate to create one for every node.
7. **Runtime and integration risk** — Examine update frequency, rendering work, asynchronous lifecycles, event ordering, focus/keyboard behavior, server/client or platform boundaries, and external dependencies. Report a performance or lifecycle issue only when code or configuration provides evidence; do not prescribe optimization from tree shape alone.
8. **Reuse and dependency direction** — Reuse is a result, not the primary reason to split. Check whether a node has a real second consumer, a stable abstraction, or a meaningful independent lifecycle. Shared low-level UI may be useful, but feature policy and product composition should remain with their owner. Look for cycles, upward dependencies, and consumers that must know implementation details.

When lenses conflict, prioritize in this order: cohesive user-facing ownership; change locality; contract clarity and direction; independent verification; reuse. Explain the trade-off instead of resolving it with a numeric score.

Completion criterion: every inspected boundary has either a concise supporting observation or a targeted limitation, and every proposed split, merge, or retained boundary has a reason grounded in a named change axis and contract.

## 4. Rank findings

Report findings rather than redesigning the entire tree. Rank them by the combination of impact, recurrence, and evidence strength:

- **Critical** — the boundary causes incorrect ownership, broken lifecycle/accessibility behavior, or a change path that is unsafe or broadly coupled.
- **High** — recurring changes cross a boundary, the contract leaks implementation detail, or the node is a major source of coordination cost.
- **Medium** — the boundary creates noticeable DX, testability, or locality friction with a contained workaround.
- **Low** — a real but localized clarity or consistency improvement with limited current impact.

Use a lower priority when evidence is weak or the improvement depends on an unconfirmed future consumer. For each finding include: stable ID, priority, location, observed evidence, diagnosis, affected change axis, DX/runtime impact, recommendation, trade-offs, and confidence. Record strengths and intentional boundaries as well as problems.

Completion criterion: every finding is independently actionable, ranked, evidence-backed, and separated from speculation; no recommendation is justified only by file length, node count, framework fashion, or theoretical reuse.

## 5. Write and validate the report

Create a Markdown report using [REPORT-TEMPLATE.md](REPORT-TEMPLATE.md) exactly. Preserve its section order and headings. Include a compact tree diagram or indented tree, a boundary summary, prioritized findings, and a recommended next slice. Use technology-neutral words in the analysis and map project-specific syntax parenthetically only when needed. Keep observed facts, inferences, recommendations, trade-offs, and unresolved questions visibly distinct.

Before finishing, run this quality pass:

- every node named in the diagram is covered by the inventory or explicitly excluded;
- every finding points to a concrete path and contract;
- every recommendation identifies the smallest useful change and what should remain intact;
- states and contracts include edge cases where evidence exists;
- accessibility, lifecycle, performance, and testability claims are evidence-backed;
- confidence is explicit and low-confidence items are not presented as mandates;
- the report has no framework-specific rule presented as universal advice.

Completion criterion: the report validates against every required section in `REPORT-TEMPLATE.md`, contains no unsupported architectural claim, and ends with the required handoff section.

## Guardrails

- Preserve existing user changes and do not edit application source while performing this review unless the user separately requests implementation.
- Prefer the smallest boundary change that improves locality or contract depth; do not split for symmetry.
- Treat props, attributes, parameters, slots, bindings, emitted events, callbacks, and context as technology-specific forms of a contract.
- Treat a tree as a runtime and maintenance boundary, not merely a file hierarchy.
- Surface uncertainty and ask for missing evidence when it materially changes the recommendation.

## Research basis

The review lenses are grounded in reusable encapsulated UI and composition: Web Components describe encapsulated behavior and template/slot composition; component-driven development emphasizes isolated states and progressive composition; modular design emphasizes separating interfaces from implementations and keeping boundaries meaningful. These sources inform the vocabulary, but repository evidence always outranks a generic pattern.

- [MDN: Web Components](https://developer.mozilla.org/en-US/docs/Web/API/Web_components)
- [MDN: Using templates and slots](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_templates_and_slots)
- [Storybook: Why Storybook?](https://storybook.js.org/docs/get-started/why-storybook)
- [Martin Fowler: Design Guidelines for Software Component Architecture](https://martinfowler.com/ieeeSoftware/designGuideline.pdf)
