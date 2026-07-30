---
name: improve-folder-architecture
description: Explore a repository's folder names, nesting, and top-level organization to assess developer experience, then present evidence-backed architecture candidates in a visual HTML report and grill through the selected candidate. Use when a user asks to review, improve, reorganize, or make a project's folder architecture more intuitive, discoverable, maintainable, or efficient for refactoring and new feature work.
disable-model-invocation: true
---

# Improve Folder Architecture

Assess folder architecture as a map of the system, not as a style contest. Help a developer or agent with no prior memory answer:

1. Where does the main product flow begin and end?
2. Where should a change for a named capability live?
3. What else is likely to change, and what should remain untouched?

Use the `codebase-design` vocabulary exactly: **module**, **interface**, **depth**, **seam**, **adapter**, **leverage**, and **locality**. Use **grilling** after the user selects a candidate. Diagnose and propose candidates; do not reorganize files or implement a refactor unless the user explicitly asks for that later.

Read [HTML-REPORT.md](HTML-REPORT.md) before generating the HTML report. It is the single source of truth for the report scaffold, visual language, card structure, diagram patterns, and tone. Do not invent a competing HTML layout.

## 1. Establish scope and evidence

Start by identifying the repository root and its conventions. Inspect, as available:

- top-level folders and files, including hidden configuration and workspace/package manifests;
- README and contributor/developer documentation;
- build, test, lint, format, and deployment entry points;
- architecture decision records, `CONTEXT.md`, ownership files, and local agent instructions;
- version-control history and recent changes, using `git log --oneline --decorate -n 80` and path statistics when useful.

Treat generated, vendored, cache, build, dependency, and coverage directories as evidence about tooling but not as product architecture. Record exclusions explicitly.

If the selected workspace has no repository, source tree, manifest, documentation, or Git history, stop architecture diagnosis. State that the scope is missing, identify the empty or unrelated paths inspected, and ask for the repository path or files. You may produce a provisional scope-recovery note, but do not fabricate architecture candidates or score the absent project.

Do not infer a folder's meaning from its name alone. Trace representative paths from an entry point through the main product flow, tests, configuration, persistence/external adapters, and documentation. Use search and targeted file reads to verify the relationship.

When routes, commands, screens, jobs, or other public entry points organize the tree, map each entry point to its implementation path before proposing capability grouping. Treat that topology as a possible navigational interface: it may let a reader move directly from public behavior to its owning module. Distinguish automatic filesystem routing from manually declared routing, but evaluate both by the same discovery task. Preserve entry-point locality unless a representative change shows that it obscures ownership or creates materially worse cross-entry-point coordination.

Completion criterion: produce a compact inventory of visible areas, entry points, conventions, exclusions, and at least two verified end-to-end change paths.

## 2. Explore the developer journey

Explore organically, prioritizing recent hotspots and the paths needed to understand the project's main flow. Use an Explore subagent when available for an independent pass, but give it the repository and user-facing objective, not a suspected answer.

Simulate a cold-start developer and an agent:

- Find the first trustworthy orientation point.
- Locate a named capability from its public behavior to its implementation and tests.
- Follow one representative request, command, job, screen, or domain flow.
- Identify likely files for a small bug fix and a new cross-cutting feature.
- Note every folder jump, ambiguous name, duplicate concept, unexplained nesting, and hidden convention.
- Check whether tests and documentation are colocated with the code they verify or discoverable by a predictable rule.

Measure friction through evidence, not taste. Prefer observations such as “one change requires edits in five unrelated top-level branches” or “two folders use the same name for different concepts” over “this feels messy.”

Evaluate a convention through the complete path and its use in the system, rather than through a single segment or a preferred naming style. Before proposing its removal, test whether it helps a developer identify the owning module, follow the relevant flow, or predict the next location to inspect. A positional or sequence-based path convention can be a useful navigational interface when it makes a stable composition, execution, or lifecycle order visible in the tree; do not dismiss it merely because another file also composes that order. Test whether the order is intentional, stable, kept in sync, and useful for a reader who sees only the tree. Do not treat similarly named code beneath different entry-point paths as accidental duplication until verifying whether each path owns a distinct presentation or lifecycle role. Treat a convention as a candidate for change only when it creates a verified conflict in a representative discovery or change task.

Apply the deletion test to proposed folders and modules: if deleting the folder would merely move the same coordination burden elsewhere, it is probably shallow. A folder earns its place when it hides meaningful complexity, gives a stable seam, or creates useful locality and leverage for a coherent capability.

Completion criterion: every finding has a concrete path, example change journey, observed friction, and confidence level; separate facts from inferences.

## 3. Generate and rank candidates

Generate a small candidate set, usually 3–5. Candidates may concern:

- top-level grouping by capability, product area, or deployment concern;
- nesting depth and whether each level communicates a useful distinction;
- names that reveal intent, ownership, lifecycle, or runtime role;
- placement of tests, fixtures, configuration, documentation, and adapters;
- duplicated or competing organizational schemes;
- entry-point-oriented paths that may be worth preserving or making more explicit;
- dependency direction and whether the tree makes seams visible;
- missing orientation artifacts when the tree alone cannot carry the needed context.

Do not prescribe a universal structure. Compare plausible alternatives against this repository's scale, change patterns, team ownership, runtime boundaries, and existing conventions. A flatter structure can be better when nesting adds no information; a deeper structure can be better when it creates locality for a high-churn capability. Capability grouping is not automatically superior to entry-point grouping: keep code with its route, command, screen, or job when that path is the clearest ownership interface, and extract a shared capability module only when shared change coordination demonstrably outweighs the loss of entry-point locality. An established convention can carry useful information even when it differs from a preferred naming scheme; preserve a stable order-encoding convention when it makes the relevant composition or lifecycle legible from the tree, unless a representative task demonstrates drift, ambiguity, or worse change locality. Recording “no structural change” is a valid conclusion when the evidence does not support a worthwhile improvement.

For each candidate, state:

- **Files/paths**: exact folders and representative files;
- **Evidence**: what a cold-start developer must do today;
- **Problem**: the lost discoverability, locality, leverage, or dependency clarity;
- **Proposal**: the smallest structural change that could address it;
- **Trade-offs**: migration cost, new ambiguity, coupling, tooling impact, and what the proposal intentionally does not solve;
- **Validation**: a falsifiable test using a representative bug fix or feature task;
- **Strength**: `Strong`, `Worth exploring`, or `Speculative`.

Rank candidates by expected improvement in cold-start comprehension and change locality, adjusted for migration risk and evidence strength. Do not turn the ranking into a numeric score unless the user asks for one.

Completion criterion: each candidate describes a materially different decision, includes a trade-off and validation test, and can be rejected without invalidating the other candidates.

## 4. Present the candidates

Write a fresh HTML report to the OS temporary directory as `folder-architecture-review-<timestamp>.html`. Resolve the directory from `$TMPDIR`, falling back to `/tmp` on Unix or `%TEMP%` on Windows. Do not write the report into the repository. Open it with the platform's default command when permitted and report the absolute path.

Follow [HTML-REPORT.md](HTML-REPORT.md) exactly. Include the scaffold's fixed header, legend, candidate card structure, and top recommendation section. Fill its placeholders with an executive orientation, evidence and assumptions, 3–5 candidate cards, and a top recommendation. Each candidate needs a before/after visualization of the tree or change path, not merely prose. Use only the diagram patterns and visual vocabulary defined there.

The report must distinguish observed current state, proposed future state, unresolved questions, and subjective choices requiring the user's decision.

End the report and the assistant message with: **Which of these would you like to explore?** Do not edit folders or propose final interfaces at this stage.

Completion criterion: the report opens successfully, contains all candidates and their evidence, names one top recommendation with rationale, and leaves implementation deferred.

## 5. Grill the selected candidate

When the user picks a candidate, invoke the `grilling` skill. Ask one decision question at a time and give a recommended answer with its rationale. Explore, in order:

1. the user journey and the change types the structure must optimize;
2. constraints from runtime, tooling, ownership, release, and migration safety;
3. the semantic unit that should own the capability;
4. the seam and dependency direction;
5. names and nesting alternatives;
6. treatment of tests, fixtures, documentation, configuration, and adapters;
7. incremental migration and rollback;
8. validation using cold-start tasks and representative changes.

Do not act on the candidate until shared understanding is confirmed. If the selected design introduces a new domain term, use `domain-modeling` to update `CONTEXT.md`. If the decision rejects a recurring alternative for a durable reason, offer an ADR only when recording it would prevent future re-litigation.

Completion criterion: the user has confirmed the chosen shape, its trade-offs, migration slice, and validation plan; only then may implementation begin.

## Guardrails

- Keep the evaluation technology-agnostic. Mention framework conventions only as repository constraints or evidence, never as universal rules.
- Prefer the smallest useful structural change. Avoid reorganizing for symmetry, aesthetics, or theoretical purity.
- Preserve existing user changes and do not modify source files during exploration.
- Treat paths as interfaces: their combined names, hierarchy, and placement should expose useful intent without requiring a caller to inspect implementation details.
- Treat the folder tree as a navigational interface. Evaluate its depth and any encoded order by the leverage it gives a reader and the locality it gives a maintainer, not by folder count or a naming preference.
- Do not replace a meaningful entry-point path with capability grouping merely for conceptual symmetry. Make the ownership and cost of any cross-entry-point shared module explicit.
- Surface ADR conflicts only when observed friction is substantial enough to justify reopening the decision.

## Evaluation lenses

Use these as lenses, not as a universal template. Verify each lens against repository evidence:

- **Cold-start discoverability**: a newcomer can orient from the root, identify the main flow, and predict where a named capability lives.
- **Entry-point locality**: a reader can move from a public route, command, screen, job, or event to its owning implementation path; shared code crosses that seam only for verified shared behavior.
- **Change locality**: a small change concentrates in a coherent path rather than scattering across unrelated branches.
- **Conceptual cohesion**: things that change together, share language, ownership, and reasons to exist are easy to find together.
- **Dependency clarity**: the tree and imports reveal which direction information and control move.
- **Naming as interface**: paths communicate purpose, role, lifecycle, scope, relationships among nearby modules, and—when deliberately encoded—stable order.
- **Convention density**: one dominant placement rule is easier to learn than several local exceptions.
- **Useful depth**: each nesting level earns its cost by adding a meaningful distinction or reducing reconstruction work.
- **Test and documentation locality**: behavior, tests, fixtures, and orientation material are discoverable through a predictable conceptual path.

Compare capability/domain-oriented, layer/technical-role-oriented, hybrid, flat, and deeper nested structures without prescribing one universally. Treat an established convention as useful evidence until a representative discovery or change task demonstrates that it harms comprehension or locality. A “no structural change” conclusion is valid.

For every finding, distinguish facts from inferences and apply the deletion test: if removing a folder merely moves the same coordination burden, it is probably a shallow organizational layer; if it hides meaningful complexity or concentrates change, it may be earning its place.
